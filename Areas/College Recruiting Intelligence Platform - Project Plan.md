# College Recruiting Intelligence Platform - Project Plan

> "Solve a painful problem for a niche audience. Everything else is noise." – JKI Playbook

## Phase 1: Foundation & Planning

### Step 1. Goal Definition with Validation

#### Core Vision
**Why:** Help student-athletes and parents make informed recruiting decisions with real-time commitment data
**Who:** Student-athletes, parents, high school coaches, recruiting consultants
**What:** Real-time recruiting intelligence platform providing strategic recommendations

#### Opportunity Discovery Method Applied
**Primary:** Scratch Your Own Itch + Niche Community Monitoring
- Track passionate recruiting communities (Twitter, recruiting forums, high school sports)
- Address information asymmetry in college recruiting

### Step 2. Practicality Filter Assessment

| Question | Yes/No | Notes |
|----------|--------|-------|
| Does this solve a painful or frequent problem? | **YES** | Recruiting is stressful, expensive, information-scarce |
| Would we pay for this if we had this problem? | **YES** | Parents spend $1000s on recruiting services |
| Can we describe the target user clearly? | **YES** | Student-athletes aged 15-18, parents, coaches |
| Can we build a simple version in 1–2 weeks? | **NO** | Complex data pipeline, ML components |
| Is someone *already* searching for this solution? | **YES** | Existing recruiting services, but data is stale |
| Can we test demand without writing code? | **YES** | Survey recruiting communities, test with spreadsheet |

**Score: 4/6 - PROCEED WITH CAUTION**
*Note: Complexity is high, but market validation is strong*

### Step 3. Idea Validation Template

#### Idea: College Recruiting Intelligence Platform
**Problem:** Student-athletes and parents lack real-time, actionable intelligence about recruiting landscapes at target universities, leading to poor targeting decisions and missed opportunities.

**Target Audience:** 
- Primary: Parents of student-athletes (decision makers, budget holders)
- Secondary: Student-athletes (15-18 years old)
- Tertiary: High school coaches, recruiting consultants

**Current Workaround:** 
- Manual social media monitoring
- Expensive recruiting services ($500-5000/year)
- Outdated roster information
- Word-of-mouth networks

**Why Now:** 
- Social media adoption in recruiting at all-time high
- API access to platforms available
- AI/ML tools make text analysis feasible
- Transfer portal creates more roster volatility

**Potential Moat:** 
- Real-time data pipeline competitive advantage
- Network effects (more users = better recommendations)
- Data quality and freshness
- Sport-specific domain expertise

**1st MVP:** 
Single-sport (football), single-platform (Twitter), manual validation, basic dashboard showing recent commitments by position/class

**Monetization:** 
- Freemium SaaS: Basic data free, advanced analytics $19/month
- Premium insights: $49/month for recruiting recommendations
- Enterprise: High school programs $199/month

**Risks:** 
- Platform API changes/restrictions
- Legal/privacy concerns with data scraping
- High infrastructure costs for data processing
- Competition from established recruiting services
- Accuracy of AI-extracted information

**Status:** idea

### Step 4. Idea Scorecard

- **Pain Level (1–5)**: 5/5 - Hair-on-fire problem for recruiting families
- **Reach (1–5)**: 4/5 - Hundreds of thousands of student-athletes annually
- **Speed to MVP (1–5)**: 2/5 - Complex system, multiple integrations needed
- **Revenue Potential (1–5)**: 4/5 - High willingness to pay in recruiting market

**Total Score: 15/20 - PROCEED**

## Phase 2: System Architecture & Technical Planning

### Step 5. Data Model Design

#### Core Entities

**Commitment**
- id, platform, post_url, post_date, extracted_date
- athlete_name, sport, position, class_year, gender
- university, confidence_score, verification_status
- raw_post_content, processed_content

**Athlete** 
- id, name, sport, position, class_year, gender
- high_school, location, social_handles
- commitment_status, commitment_date

**University**
- id, name, division, conference, location
- sport_programs[], roster_limits by sport

**Roster**
- university_id, sport, season, position
- current_count, available_spots, last_updated

**RecruitingRecommendation**
- athlete_profile, target_universities[]
- recommendation_score, reasoning, generated_date

### Step 6. Technical Stack Selection

**Data Pipeline:**
- Python (tweepy, instagram-basic-display-api)
- Apache Airflow for scheduling
- PostgreSQL for structured data
- Redis for caching and deduplication

**AI/ML Processing:**
- OpenAI/Claude for text extraction
- spaCy for named entity recognition
- Custom classification models

**Backend API:**
- FastAPI (Python)
- Pydantic for data validation
- SQLAlchemy ORM

**Frontend:**
- Next.js/React
- Tailwind CSS
- Charts.js for analytics

**Infrastructure:**
- Docker containerization
- AWS/DigitalOcean hosting
- GitHub Actions CI/CD

## Phase 3: Development Strategy

### Step 7. MVP Definition (Ruthlessly Scoped)

**MVP Scope - "Football Twitter Tracker":**
1. Twitter API integration for keyword monitoring
2. Single sport focus: Football
3. Manual validation workflow for extracted data
4. Basic web dashboard showing recent commitments
5. Simple filtering by position/class year
6. No recommendations yet - just data display

**MVP User Stories:**
- User can view recent football commitments from Twitter
- User can filter commitments by position
- User can filter commitments by class year
- User can see commitment details (name, school, position)
- Admin can review and validate extracted data

**MVP Success Metrics:**
- 50+ commitments accurately extracted per week
- 85%+ accuracy in sport/position/school extraction
- 10+ active users testing the dashboard

### Step 8. Implementation Sections

#### Section 1: Data Ingestion Foundation
**Status:** Not Started
- [ ] Twitter API integration and authentication
- [ ] Basic keyword monitoring setup
- [ ] Raw post storage in database
- [ ] Deduplication logic implementation
- [ ] Basic logging and error handling

#### Section 2: AI Text Processing Pipeline
**Status:** Not Started  
- [ ] LLM integration for information extraction
- [ ] Prompt engineering for commitment detection
- [ ] Named entity recognition for athlete/school names
- [ ] Classification for sport/position/gender
- [ ] Confidence scoring system

#### Section 3: Data Validation & Quality
**Status:** Not Started
- [ ] Manual validation interface
- [ ] Confidence threshold tuning
- [ ] False positive detection
- [ ] Data cleaning workflows
- [ ] Quality metrics dashboard

#### Section 4: Basic Dashboard
**Status:** Not Started
- [ ] Web frontend setup
- [ ] Commitment listing interface
- [ ] Basic filtering functionality
- [ ] Responsive design implementation
- [ ] User authentication (basic)

#### Section 5: System Reliability
**Status:** Not Started
- [ ] Automated testing suite
- [ ] Monitoring and alerting
- [ ] Backup and recovery procedures
- [ ] Performance optimization
- [ ] Documentation completion

## Phase 4: Post-MVP Expansion (Icebox)

### Future Phases (After MVP Validation):
1. **Multi-Sport Expansion:** Basketball, baseball, soccer
2. **Instagram Integration:** Expand to second platform
3. **Roster Intelligence:** University roster tracking
4. **Recommendation Engine:** AI-powered targeting suggestions
5. **Advanced Analytics:** Market insights, trend analysis
6. **Mobile App:** Native iOS/Android applications

### Cool Ideas (Validate First):
- Transfer portal tracking
- High school athlete database
- Recruiting timeline predictions
- Social media influence scoring
- Parent community features

## Phase 5: Validation & Testing Strategy

### Pre-Development Validation:
1. **Survey recruiting communities** (Reddit r/collegebaseball, recruiting Twitter)
2. **Interview 10 recruiting families** about current pain points
3. **Analyze competitor offerings** (247Sports, Rivals, etc.)
4. **Test manual extraction process** with sample posts
5. **Validate willingness to pay** with landing page + email collection

### Development Validation:
1. **Weekly accuracy reviews** with domain experts
2. **User testing sessions** with recruiting families
3. **Performance benchmarking** against manual processes
4. **Cost analysis** of API usage and infrastructure
5. **Legal review** of data collection practices

## Key Success Factors

1. **Start with manual validation** - Don't trust AI extraction initially
2. **Focus on data quality over quantity** - Better to have 100 accurate records than 1000 questionable ones
3. **Build domain expertise** - Understand recruiting cycles, signing periods, transfer rules
4. **Engage the community early** - Recruiting community is passionate and vocal
5. **Plan for scale** - Social media data volumes can explode quickly
6. **Legal compliance** - Platform ToS, data privacy, minor protection laws

## Risk Mitigation

1. **Platform API Risk:** Build multi-platform from start, maintain fallback methods
2. **Accuracy Risk:** Implement confidence scoring, manual validation workflows
3. **Cost Risk:** Monitor API usage, implement rate limiting, optimize queries
4. **Competition Risk:** Focus on real-time advantage, superior UX
5. **Legal Risk:** Consult attorney on data collection, implement proper consent flows

---

**Next Steps:**
1. Validate market demand through surveys and interviews
2. Build manual prototype using spreadsheets
3. Test information extraction accuracy with sample data
4. Create technical architecture documents
5. Set up development environment and begin Section 1

*Status: Ready for market validation phase*
