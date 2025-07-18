# Manual Extraction Test Framework

## Objective
Test the accuracy of AI/LLM extraction of athlete commitment information from social media posts to validate technical feasibility before building automated systems.

---

## Test Setup

### Sample Collection
**Target:** 100 commitment posts across platforms
- **Twitter:** 60 posts (primary focus)
- **Instagram:** 40 posts (secondary)

**Sports Distribution:**
- Football: 30 posts
- Basketball: 25 posts  
- Baseball: 20 posts
- Soccer: 15 posts
- Other sports: 10 posts

**Post Types:**
- Original commitment announcements: 70%
- Retweets/shares by schools: 20%
- Media coverage posts: 10%

### Data Collection Sources
1. **Twitter Search Terms:**
   - "committed to" + university names
   - "#committed" hashtag
   - "blessed to announce" 
   - "excited to continue"
   - Sport-specific hashtags (#D1Football, #BaseballCommit)

2. **Instagram Search:**
   - Location tags at universities
   - Sport-specific hashtags
   - University athletic department accounts

---

## Information Extraction Framework

### Required Fields to Extract
1. **Athlete Information:**
   - Full name
   - Gender (M/F)
   - Class year (2025, 2026, etc.)
   - Position/event
   - High school name
   - Location (city, state)

2. **Commitment Details:**
   - University committed to
   - Sport
   - Division level (D1, D2, D3, NAIA, JUCO)
   - Scholarship status (if mentioned)
   - Commitment date

3. **Post Metadata:**
   - Platform (Twitter/Instagram)
   - Post URL
   - Post date/time
   - Original poster (athlete, parent, coach, media)
   - Engagement metrics (likes, shares, comments)

---

## Testing Process

### Phase 1: Manual Baseline (Gold Standard)
**Process:**
1. Manually review 100 sample posts
2. Extract all information fields by hand
3. Create "ground truth" dataset
4. Note ambiguous cases and edge cases
5. Document extraction confidence levels

**Time Estimate:** 4-6 hours total

### Phase 2: LLM Extraction Testing
**Tools to Test:**
- OpenAI GPT-4
- Claude 3.5 Sonnet
- Gemini Pro

**Process:**
1. Create standardized prompt for each LLM
2. Process same 100 posts through each model
3. Compare extracted data to manual baseline
4. Calculate accuracy metrics for each field
5. Identify patterns in errors/failures

### Phase 3: Accuracy Analysis
**Metrics to Calculate:**
- Overall accuracy percentage
- Field-by-field accuracy rates
- False positive rate (detecting commitments that aren't)
- False negative rate (missing actual commitments)
- Confidence correlation (high confidence = higher accuracy?)

---

## Sample Extraction Prompt

```
You are an expert at extracting college recruiting commitment information from social media posts. 

Analyze this post and extract the following information in JSON format:

{
  "is_commitment": true/false,
  "athlete_name": "full name or null",
  "gender": "M/F or null", 
  "class_year": "2024/2025/2026/2027 or null",
  "position": "position/event or null",
  "high_school": "school name or null",
  "location": "city, state or null",
  "university": "committed university or null",
  "sport": "sport name or null",
  "division": "D1/D2/D3/NAIA/JUCO or null",
  "scholarship_mentioned": true/false,
  "confidence_score": 1-10,
  "reasoning": "brief explanation of extraction"
}

POST: [insert post text here]
```

---

## Test Results Template

### Post Analysis Results
| Post ID | Manual Extract | GPT-4 Extract | Claude Extract | Gemini Extract | Notes |
|---------|---------------|---------------|----------------|----------------|-------|
| 001     | [data]        | [data]        | [data]         | [data]         | [observations] |
| 002     | [data]        | [data]        | [data]         | [data]         | [observations] |

### Accuracy Summary
| Field | Manual Baseline | GPT-4 Accuracy | Claude Accuracy | Gemini Accuracy |
|-------|----------------|----------------|-----------------|-----------------|
| Athlete Name | 100% | 85% | 88% | 82% |
| University | 100% | 92% | 94% | 89% |
| Sport | 100% | 96% | 95% | 94% |
| Position | 100% | 78% | 80% | 75% |
| Class Year | 100% | 71% | 73% | 68% |
| Gender | 100% | 89% | 91% | 87% |

### Common Error Patterns
1. **Name Variations:** Nicknames vs. full names
2. **Position Ambiguity:** Multiple positions listed
3. **Class Year Confusion:** Graduation year vs. recruiting class
4. **University Abbreviations:** School nicknames vs. official names
5. **Implicit Information:** Details mentioned in images/context

---

## Success Criteria

### Minimum Viable Accuracy Thresholds:
- **Overall commitment detection:** 90%+
- **University identification:** 85%+
- **Sport identification:** 90%+
- **Athlete name extraction:** 80%+
- **Position extraction:** 70%+
- **Class year extraction:** 70%+

### Go/No-Go Decision Matrix:
- **Green (Proceed):** All core fields >75% accuracy
- **Yellow (Refine):** Core fields 60-75%, refinement possible
- **Red (Pivot):** Core fields <60%, fundamental approach change needed

---

## Implementation Notes

### Data Collection Tools:
- Twitter API v2 (Academic Research access preferred)
- Instagram Basic Display API
- Manual screenshot collection as backup
- Browser automation tools (Selenium/Playwright)

### Quality Control:
- Double-check manual extractions with second reviewer
- Random sampling for validation
- Edge case documentation
- Ambiguous case handling protocols

### Next Steps Based on Results:
1. **If successful (>75% accuracy):** Proceed to MVP development
2. **If marginal (60-75%):** Refine prompts, test additional models
3. **If unsuccessful (<60%):** Consider hybrid approach or pivot strategy

---

## Timeline: 1 Week
- **Days 1-2:** Sample collection and organization
- **Days 3-4:** Manual extraction (ground truth)
- **Days 5-6:** LLM testing and comparison
- **Day 7:** Analysis and decision on technical feasibility
