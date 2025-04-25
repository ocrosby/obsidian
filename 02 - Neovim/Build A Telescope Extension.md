
## Requirements

Make sure you have neovim and telescope installed.

## Example

```lua
local pickers = require "telescope.pickers"
local finders = require "telescope.finders"
local conf = require("telescope.config").values

--it's good practice to pass in opts
local example = function(opts)
	opts = opts = {}
    --create a new picker
	pickers.new(opts, {
		finder = finders.new_table {
			results = { "qa", "prod", "legacy" }
		},
		sorter = conf.generic_sorter(opts),
	}):find()
end

example()
```

## Picker

A picker contains a list of items supplied by a finder.

## Finder

## Sorter

A sorter configures sorting for the picker.

## Actions

When an element is selected there is an event that triggers an action that does something with the selection.

`Action == Hitting Enter` for example

## Running the extension

With the new extension in the active buffer enter normal mode and

```plaintext
:luafile %
```

In the example you should see a picker that displays `qa`, `prod`, `legacy`. When you select one you should see a new buffer named whatever you selected.

```lua
-- File: lua/telescope/_extensions/pytest_runner.lua

local pickers = require("telescope.pickers")
local finders = require("telescope.finders")
local conf = require("telescope.config").values
local actions = require("telescope.actions")
local action_state = require("telescope.actions.state")
local Path = require("plenary.path")

local M = {}

-- Preset list of environments
local environments = { "qa", "prod", "legacy" }

-- Preset list of regions
local regions = { "auto", "use1", "usw2", "euw1", "apse1" }

--- Read pytest markers from pytest.ini if it exists.
-- Looks for lines with `-m "<markers>"` inside `addopts`
-- Returns a sorted list of unique marker strings
local function read_markers_from_ini()
  local markers_set = {}
  local ini = Path:new("pytest.ini")

  if ini:exists() then
    for _, line in ipairs(ini:readlines()) do
      local opts = line:match("addopts.*-m%s+\"(.-)\"")
      if opts then
        for marker in opts:gmatch("[^%s]+") do
          markers_set[marker] = true
        end
      end
    end
  end

  -- Convert set to sorted list
  local markers = vim.tbl_keys(markers_set)
  table.sort(markers)
  return markers
end

--- Prompt user to select one or more markers from pytest.ini.
-- Once selected, builds and displays the full command
-- with selected environment, region, and markers if any.
local function select_markers(env, region)
  local markers = read_markers_from_ini()

  pickers.new({}, {
    prompt_title = "Select Markers",
    finder = finders.new_table {
      results = markers,
    },
    sorter = conf.generic_sorter({}),
    attach_mappings = function(prompt_bufnr, _)
      actions.select_default:replace(function()
        local selections = {}
        for _, entry in ipairs(action_state.get_selected_entries()) do
          table.insert(selections, entry.value)
        end
        actions.close(prompt_bufnr)

        -- Build the base command
        local command = string.format(
          'python3 preprocessor.py -e %s -r %s',
          env,
          region
        )

        -- If markers are selected, append the -m option
        if #selections > 0 then
          local marker_str = table.concat(selections, "\" and \"")
          if #selections > 1 then
            marker_str = "\"" .. marker_str .. "\""
          else
            marker_str = selections[1]
          end
          command = command .. string.format(' -m %s', marker_str)
        end

        vim.notify("Generated command:\n" .. command, vim.log.levels.INFO)
      end)

      return true
    end
  }):find()
end

--- Prompt user to select a region after selecting environment.
-- Once selected, proceeds to marker selection.
local function select_region(env)
  pickers.new({}, {
    prompt_title = "Select Region",
    finder = finders.new_table {
      results = regions,
    },
    sorter = conf.generic_sorter({}),
    default_selection_index = 1,
    attach_mappings = function(prompt_bufnr, _)
      actions.select_default:replace(function()
        local selection = action_state.get_selected_entry()
        actions.close(prompt_bufnr)
        select_markers(env, selection.value)
      end)
      return true
    end
  }):find()
end

--- Entry point for the Telescope extension.
-- Prompts user to select an environment, then proceeds to region.
function M.run_pytest_picker()
  pickers.new({}, {
    prompt_title = "Select Environment",
    finder = finders.new_table {
      results = environments,
    },
    sorter = conf.generic_sorter({}),
    default_selection_index = 1,
    attach_mappings = function(prompt_bufnr, _)
      actions.select_default:replace(function()
        local selection = action_state.get_selected_entry()
        actions.close(prompt_bufnr)
        select_region(selection.value)
      end)
      return true
    end
  }):find()
end

-- Register the extension with Telescope
return require("telescope").register_extension({
  exports = {
    pytest_runner = M.run_pytest_picker,
  },
})
```