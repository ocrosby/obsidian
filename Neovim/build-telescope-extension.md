
# Build a Telescope Plugin

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
local yaml = require("yaml")  -- Assume you have a Lua YAML parser available

local M = {}

-- Default presets
local default_environments = { "qa", "prod", "legacy" }
local default_regions = { "auto", "use1", "usw2", "euw1", "apse1" }

--- Read ingress-mapping.yaml if it exists.
local function read_ingress_mapping()
  local ingress_file = Path:new("ingress-mapping.yaml")
  if not ingress_file:exists() then
    return nil
  end

  local content = ingress_file:read()
  local parsed = yaml.load(content)
  return parsed
end

--- Get list of environments dynamically or fall back
local function get_environments()
  local ingress = read_ingress_mapping()
  if ingress and ingress.environments then
    local envs = {}
    for _, env in ipairs(ingress.environments) do
      table.insert(envs, env.name)
    end
    return envs
  end
  return default_environments
end

--- Get list of regions for a selected environment dynamically or fallback
local function get_regions_for_environment(selected_env)
  local ingress = read_ingress_mapping()
  if ingress and ingress.environments then
    for _, env in ipairs(ingress.environments) do
      if env.name == selected_env then
        local regions = {}
        for _, region in ipairs(env.regions or {}) do
          table.insert(regions, region.name)
        end
        return regions
      end
    end
  end
  return default_regions
end

--- Read pytest markers from pytest.ini
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

  local markers = vim.tbl_keys(markers_set)
  table.sort(markers)
  return markers
end

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

        local command = string.format('python3 preprocessor.py -e %s -r %s', env, region)

        if #selections > 0 then
          local marker_str = table.concat(selections, " and ")
          marker_str = '"' .. marker_str .. '"'
          command = command .. string.format(' -m %s', marker_str)
        end

        vim.notify("Generated command:\n" .. command, vim.log.levels.INFO)
        vim.fn.setreg('+', command)  -- Copy command to clipboard
      end)

      return true
    end
  }):find()
end

local function select_region(env)
  local regions = get_regions_for_environment(env)

  pickers.new({}, {
    prompt_title = "Select Region",
    finder = finders.new_table {
      results = regions,
    },
    sorter = conf.generic_sorter({}),
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

function M.run_pytest_picker()
  local envs = get_environments()

  pickers.new({}, {
    prompt_title = "Select Environment",
    finder = finders.new_table {
      results = envs,
    },
    sorter = conf.generic_sorter({}),
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

return require("telescope").register_extension({
  exports = {
    pytest_runner = M.run_pytest_picker,
  },
})
```