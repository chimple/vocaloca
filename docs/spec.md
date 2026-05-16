Below is a product + technical spec for the offline Flutter language-learning app, followed by a fine-grained phased development plan. I am assuming the first product is **English conversation learning for Indian-language learners**, with future expansion to any language pair.

This spec builds on Chimple’s current product direction: offline-first learner modules, PAL-based personalized pathways, teacher visibility, GenAI-supported content creation, and continuous feedback loops across learner, teacher, and program layers. Chimple’s internal product materials describe the Kids app as offline-first, teacher console as progress/assignment-facing, and PAL as the engine for scaffolding and immediate feedback; GenAI Studio is already positioned for localized, curriculum-aligned content generation with human review. 

---

# 1. Product name

Working title: **VocaLoca**

Tagline: **Learn to speak, read, and write English through real conversations.**

---

# 2. Product goal

Build a fully offline, AI-powered language-learning app where learners practice English through scenario-based conversations with two AI characters:

1. **Coach character**: Voca, who guides, corrects, translates, gives hints, and tracks learning.
2. **Role-play character**: Loca: shopkeeper, teacher, doctor, bus conductor, friend, interviewer, etc.

The app should support:

* English learning from Indian languages first;
* conversational speaking practice;
* reading and writing linked to every speaking concept;
* CEFR-aligned progression;
* local Gemma 4 using `flutter_gemma`;
* offline RAG;
* multiple learner profiles on one device;
* local learner model, mastery tracking, and generated-content cache.

---

# 3. Target users

## Primary learners

| Segment                 | Needs                                                                   |
| ----------------------- | ----------------------------------------------------------------------- |
| Children, upper primary | Confidence, pronunciation, simple conversation, reading/writing support |
| Adolescents             | School, interview, daily-life English, explanation and opinion skills   |
| Adults                  | Practical English for work, travel, services, self-expression           |
| Low-literacy learners   | Audio-first, picture-based, bilingual scaffolding                       |

## Secondary users

| User            | Needs                                                     |
| --------------- | --------------------------------------------------------- |
| Teachers        | Assign missions, track progress, identify weak learners   |
| Parents         | Understand child’s progress simply                        |
| Program teams   | Usage, completion, learning progress, deployment evidence |
| Curriculum team | Create and review content packs                           |

---

# 4. Core product principles

1. **Conversation first, curriculum grounded**
   Learners should feel they are speaking in real situations, but every conversation must map to CEFR can-dos, vocabulary, grammar, pronunciation, reading, and writing.

2. **Offline by default**
   The app should work without internet after installation/model/content provisioning. Chimple’s existing design direction emphasizes downloadable learning modules and offline use with later syncing where available. 

3. **AI supports learning, but does not own progression**
   Gemma generates dialogue, hints, and corrections. The app owns CEFR progression, mastery, safety, rewards, and assessment.

4. **Two-character pedagogy**
   The role-play character creates the need to speak. The coach helps the learner succeed.

5. **Four-skill loop**
   Every concept should move through: **listen → speak → read → write → apply again**.

6. **Child-safe and data-minimizing**
   Chimple’s internal AI ethics language emphasizes child protection, learner-data minimization, human review of AI-generated content, and authorized access to student data. 

---

# 5. MVP scope

## MVP language pair

* Target language: English
* Home-language support: start with one language, preferably Hindi or Kannada, then add others
* Architecture should support: Hindi, Kannada, Tamil, Telugu, Marathi, Portuguese, etc.

## MVP CEFR range

* Pre-A1
* A1.1
* A1.2
* A2.1

B1 can be architected but not fully built in MVP.

## MVP scenarios

Start with 8–10 high-frequency scenarios:

1. Introduce yourself
2. My family
3. My school
4. At the bus stop
5. At the market
6. Asking for help
7. At the doctor
8. Talking to a teacher
9. Describing a picture
10. Free talk about hobbies

## MVP activity types

* Guided role-play
* Coach correction
* Repeat-after-me
* Hint / translation
* Vocabulary cards
* Reading dialogue
* Fill-in-the-blank writing
* Sentence builder
* Short session summary
* Daily mission
* Multiple profiles
* Local progress tracking

---

# 6. Key features

## 6.1 Multi-profile onboarding

Each device can host multiple learners.

### Required fields

* Learner name or nickname
* Avatar
* Age band
* Home language
* Target language
* Starting level: unknown / beginner / some English
* Preferred support: more home language / balanced / mostly English

### Placement

MVP placement should be simple:

* Can repeat words?
* Can answer name/age?
* Can understand a simple prompt?
* Can read a simple sentence?
* Can write/copy a simple sentence?

Output:

```json
{
  "speaking_level": "Pre-A1",
  "listening_level": "A1.1",
  "reading_level": "Pre-A1",
  "writing_level": "Pre-A1",
  "recommended_start": "introduce_yourself_a1_1"
}
```

---

## 6.2 Home screen

The home screen should answer: **“What should I do now?”**

### Home screen sections

1. AI coach greeting
2. Today’s mission
3. Daily speaking goal
4. Continue path
5. Quick practice
6. Weak words/concepts
7. Rewards/progress
8. Profile switcher

Example:

```text
Hi Asha 👋
Learning English from Hindi        Level A1.1

[Coach]
Today let’s practice asking for a bus ticket.

[Start Today’s Mission]

Today’s Mission
🚌 At the Bus Stop
Goal: Ask where the bus goes
Skills: Speak • Read • Write
Time: 5 min

Quick Practice:
Talk Now | Role Play | Camera Talk | My Words | Read & Write

Practice Again:
ticket • station • “Does this bus go...?”
```

---

## 6.3 Two-character role-play

Every scenario has:

* role-play character;
* coach character;
* learner;
* current CEFR goal;
* target phrase;
* vocabulary;
* grammar focus.

Example:

**Bus conductor:** “Where do you want to go?”
**Learner:** “This bus go school?”
**Coach:** “Good try. Say: ‘Does this bus go to school?’ Try again.”
**Learner:** “Does this bus go to school?”
**Bus conductor:** “Yes, it goes to school.”

### Correction modes

| Mode                 | Use case                               |
| -------------------- | -------------------------------------- |
| Immediate correction | Meaning breaks or target concept error |
| Gentle recast        | Meaning clear but sentence can improve |
| End feedback         | Higher-level learner, fluency-focused  |
| Silent coach         | Challenge mode                         |

---

## 6.4 CEFR / can-do curriculum engine

The app stores a structured curriculum spine.

### Data model

```json
{
  "can_do_id": "a1_bus_ask_route",
  "cefr_level": "A1.2",
  "skill": "spoken_interaction",
  "scenario": "bus_stop",
  "can_do": "I can ask whether a bus goes to a place.",
  "target_phrases": [
    "Does this bus go to school?",
    "I want to go to the station."
  ],
  "vocabulary": ["bus", "stop", "ticket", "school", "station"],
  "grammar_focus": ["yes/no question with does"],
  "reading_task_id": "read_bus_dialogue_a1",
  "writing_task_id": "write_bus_question_a1",
  "mastery_rule": "Use the target question independently in 3 role-play turns."
}
```

### First curriculum pack

Build 60–80 can-dos:

| Level   | Approx. can-dos |
| ------- | --------------: |
| Pre-A1  |              10 |
| A1.1    |              15 |
| A1.2    |              15 |
| A2.1    |              15 |
| A2.2    |              10 |
| B1 seed |            5–10 |

---

## 6.5 Offline RAG

RAG retrieves a compact lesson packet for Gemma.

### RAG stores

| Store               | Contents                                         |
| ------------------- | ------------------------------------------------ |
| Curriculum store    | CEFR, can-dos, scenarios, grammar, vocabulary    |
| Scenario store      | Role characters, scene prompts, cultural context |
| Vocabulary store    | Word meaning, examples, pronunciation notes      |
| Grammar/error store | Common errors, corrections, micro-drills         |
| Learner memory      | Weak words, recent errors, mastery               |
| Content store       | Approved dialogues, reading/writing tasks        |
| Safety store        | Child-safe constraints and fallback rules        |

### RAG output

```json
{
  "learner": {
    "home_language": "Hindi",
    "speaking_level": "A1.1",
    "weak_words": ["ticket", "station"],
    "recent_errors": ["This bus go school?"]
  },
  "mission": {
    "scenario": "bus_stop",
    "can_do": "Ask whether a bus goes to a place",
    "target_phrase": "Does this bus go to school?",
    "grammar_focus": "yes/no question with does"
  },
  "characters": {
    "role_character": "bus conductor",
    "coach_character": "Chimple"
  },
  "instruction": {
    "correction_mode": "gentle",
    "max_words": 25,
    "use_home_language_support": true
  }
}
```

---

## 6.6 Local Gemma 4 using `flutter_gemma`

Use the `flutter_gemma` package as the local LLM runtime. The package currently describes support for running Gemma and other LLMs locally across Android, iOS, Web, and desktop, with local execution, multimodal support, GPU acceleration, text embeddings, and offline functionality. It also lists Gemma 4 support, `.litertlm` support for newer models, and `ModelType.gemma4` for Gemma 4 models. ([Dart packages][1])

### App abstraction

Do not call `flutter_gemma` directly from UI screens. Wrap it:

```dart
abstract class LocalTutorService {
  Future<TutorResponse> generateRoleplayTurn(LessonContext context);
  Future<TutorResponse> generateCoachCorrection(
    LessonContext context,
    String learnerUtterance,
  );
  Future<ReadingActivity> generateReadingFollowup(
    LessonContext context,
    ConversationTranscript transcript,
  );
  Future<WritingFeedback> generateWritingFeedback(
    LessonContext context,
    String learnerWriting,
  );
}
```

### Important platform constraints

The package page notes that Android full support is `arm64-v8a`, while `.litertlm` FFI, embeddings, and image generation are arm64-only on Android. It also documents iOS setup requirements, including iOS 16 for MediaPipe GenAI. ([Dart packages][1])

Therefore the app should support:

| Runtime mode      | Behavior                                                            |
| ----------------- | ------------------------------------------------------------------- |
| Full AI mode      | Gemma role-play, correction, RAG, generated reading/writing         |
| Lite mode         | Pre-generated content, rule-based corrections, no open conversation |
| Template fallback | Deterministic hints/corrections when model unavailable              |

---

## 6.7 Speech

MVP can be staged.

### Phase 1 speech

* Record learner audio
* Use manual text input or mocked STT for development
* TTS for AI lines if offline TTS available
* Store transcript and activity events

### Phase 2 speech

* Offline STT
* Offline TTS
* Simple pronunciation scoring:

  * word matched;
  * speaking duration;
  * retries;
  * confidence;
  * skipped words.

### Phase 3 speech

* Word-level pronunciation feedback
* Fluency score
* Common pronunciation-gap detection

---

## 6.8 Reading and writing

Every role-play mission should unlock reading and writing follow-up.

### Reading activities

* Read after listening
* Read aloud
* Dialogue reading
* Read and answer
* Word discovery
* Picture reading

### Writing activities

* Trace / copy
* Fill blanks
* Sentence builder
* Dictation
* Speak-to-write correction
* Guided writing
* Free writing for A2+

Example for bus stop:

```json
{
  "reading": [
    "Asha is at the bus stop.",
    "She wants to go to school.",
    "She asks, “Does this bus go to school?”"
  ],
  "writing": [
    {
      "type": "sentence_builder",
      "prompt": "Does / this / bus / go / to / school?",
      "answer": "Does this bus go to school?"
    }
  ]
}
```

---

## 6.9 Learner model and PAL

The local PAL engine recommends the next mission based on:

* current CEFR/can-do progress;
* weak vocabulary;
* repeated grammar errors;
* pronunciation issues;
* reading/writing level;
* teacher assignment;
* spaced repetition schedule.

Chimple’s technical requirements describe PAL as a personalization layer with pluggable algorithms, autonomous assessments, learning analytics, content mapping to curricula and learning objectives, and offline rules that clients can download and operate locally before syncing when available. 

### MVP PAL can be rule-based

Start simple:

```text
If target concept failed twice:
  assign remediation
Else if mission completed:
  assign next mission in same scenario or next can-do
Else if weak words due:
  assign vocabulary review
Else:
  continue learning path
```

Later, add knowledge tracing or bandit-based recommendation.

---

## 6.10 Safety

### Local safety controls

* Input safety classifier or rules
* Output schema validation
* Safe-topic whitelist by age band
* No personal-data prompts
* No identity inference from camera
* Safe fallback messages
* Human-reviewed base content packs

Chimple’s AI usage notes already position AI-generated content as human-reviewable and aligned to curriculum, age, and cultural relevance before use. 

---

## 6.11 Data and analytics

Store local events even if the app is fully offline.

### Event types

* profile_created
* mission_started
* turn_completed
* learner_utterance
* correction_given
* retry_success
* reading_completed
* writing_completed
* word_practiced
* concept_mastery_updated
* session_summary_created

### Example event

```json
{
  "profile_id": "asha_001",
  "event_type": "correction_given",
  "mission_id": "bus_stop_a1_ask_route",
  "concept_id": "question_formation_does",
  "learner_text": "This bus go school?",
  "corrected_text": "Does this bus go to school?",
  "result": "needs_retry",
  "timestamp": "local"
}
```

---

# 7. Technical architecture

```text
Flutter App
  ├── Profile Manager
  ├── Home / Mission / Role-play / Read-Write UI
  ├── Mission Orchestrator
  ├── Local PAL Engine
  ├── Assessment Engine
  ├── Offline RAG
  ├── LocalTutorService
  │     └── flutter_gemma
  ├── Offline Speech Services
  ├── SQLite / Drift DB
  ├── Content Packs
  ├── Local Vector Index
  ├── Generated Content Cache
  └── Optional Export / Sync
```

## Suggested storage

```text
/app_data/
  models/
    gemma4/
      model.litertlm
      tokenizer.model
      model_manifest.json

  content_packs/
    hi_en_pre_a1_a2/
      curriculum.sqlite
      scenarios.json
      vocabulary.json
      grammar.json
      reading_writing.json
      vector.index
      safety_rules.json

  profiles/
    app.db
    media/
      profile_asha/
      profile_ravi/

  cache/
    generated_content_cache.sqlite

  logs/
    local_events.jsonl
```

---

# 8. Local database tables

```text
profiles
profile_settings
curriculum_items
missions
scenario_templates
vocabulary_items
grammar_concepts
learner_vocabulary
learner_mastery
conversation_sessions
conversation_turns
reading_attempts
writing_attempts
activity_events
generated_content_cache
rewards
content_pack_versions
```

Every learner-specific table must include `profile_id`.

---

# 9. Prompt families

Use separate prompts for separate tasks.

| Prompt               | Purpose                    |
| -------------------- | -------------------------- |
| Global system prompt | Child-safe tutor rules     |
| Role-play turn       | Role character speaks      |
| Coach correction     | Correct learner utterance  |
| Hint                 | Help learner when stuck    |
| Utterance analysis   | Classify learner errors    |
| Remediation drill    | Practice repeated mistakes |
| Reading follow-up    | Generate short passage     |
| Writing follow-up    | Generate writing task      |
| Writing feedback     | Correct learner writing    |
| Session summary      | Summarize progress         |
| Safety check         | Validate output            |

All outputs should be JSON, not free text.

---

# 10. Non-functional requirements

| Area               | Requirement                                                   |
| ------------------ | ------------------------------------------------------------- |
| Offline            | Core app, model, content, progress must work without internet |
| Multi-profile      | At least 5 profiles per device in MVP                         |
| Performance        | First AI response target under 5–8 sec on supported devices   |
| Low-end mode       | Template fallback if model is slow/unavailable                |
| Privacy            | No cloud dependency for learner data                          |
| Safety             | Child-safe output validation before display                   |
| Extensibility      | Add language pairs through content packs                      |
| Observability      | Local debug logs and prompt trace in developer mode           |
| Content versioning | Curriculum/content packs versioned and migratable             |
| Accessibility      | Audio-first, large buttons, low reading load                  |

---

# 11. Fine-grained phased development plan

The phases below are intentionally small so the team can ship, test, and iterate.

---

## Phase 0: Product definition and constraints

### 0.1 Define MVP learner segment

**Output:** one-page MVP user definition.

Decisions:

* Age band
* First home language
* First CEFR scope
* Supported devices
* Whether first test is tablet or phone

### 0.2 Define success metrics

**Output:** product metrics sheet.

MVP metrics:

* mission completion rate;
* average speaking minutes;
* retry success rate;
* weak-word improvement;
* D1/D7 retention;
* teacher/parent feedback;
* latency on target devices.

### 0.3 Define device baseline

**Output:** supported-device matrix.

Include:

* RAM;
* storage;
* Android version;
* CPU architecture;
* whether Gemma full mode is supported;
* fallback mode behavior.

---

## Phase 1: App skeleton

### 1.1 Flutter project setup

Build:

* feature-first folder structure;
* routing;
* theme;
* localization skeleton;
* environment config.

Acceptance:

* app launches on Android;
* empty Home screen loads;
* navigation works.

### 1.2 State management setup

Build:

* Riverpod;
* app state;
* profile state;
* mission state.

Acceptance:

* mock profile state can switch between learners.

### 1.3 Local database setup

Build:

* Drift/SQLite;
* migrations;
* basic DAOs.

Acceptance:

* app creates local DB;
* can insert/read profile;
* migration test passes.

---

## Phase 2: Multi-profile foundation

### 2.1 Profile creation

Build:

* create learner profile;
* avatar selection;
* home language;
* target language;
* age band.

Acceptance:

* multiple profiles can be created locally.

### 2.2 Profile switcher

Build:

* switch profile from home screen;
* profile-scoped app state.

Acceptance:

* changing profile changes progress/home screen data.

### 2.3 Profile data isolation

Build:

* `profile_id` enforced across learner tables.

Acceptance:

* learner A’s mission history does not appear for learner B.

---

## Phase 3: Static curriculum spine

### 3.1 Define content schema

Build:

* CEFR/can-do schema;
* scenario schema;
* vocabulary schema;
* grammar schema;
* mission schema.

Acceptance:

* schemas validated through tests.

### 3.2 Create first mini content pack

Build 5 missions:

1. Introduce yourself
2. Say what you like
3. Ask name
4. Bus stop
5. Market

Acceptance:

* app can load local content pack.

### 3.3 Curriculum repository

Build:

* local repository for missions;
* query by level, scenario, skill.

Acceptance:

* retrieve next mission for a profile.

---

## Phase 4: Basic home screen

### 4.1 Home screen layout

Build:

* coach greeting;
* today’s mission;
* quick practice cards;
* profile switcher.

Acceptance:

* home screen uses active profile and curriculum data.

### 4.2 Daily goal placeholder

Build:

* local daily speaking goal;
* progress card.

Acceptance:

* displays progress even before speech integration.

### 4.3 Weak words placeholder

Build:

* display weak words from local DB.

Acceptance:

* seeded weak words appear on home screen.

---

## Phase 5: Mission player without AI

### 5.1 Mission state machine

Build deterministic mission flow:

```text
intro → model line → learner response → feedback → reading → writing → summary
```

Acceptance:

* one mission can be completed using canned content.

### 5.2 Role-play screen

Build:

* role character bubble;
* coach bubble;
* learner response area;
* continue/retry buttons.

Acceptance:

* canned role-play works from start to finish.

### 5.3 Event logging

Build:

* mission_started;
* turn_completed;
* mission_completed.

Acceptance:

* local event log records all mission steps.

---

## Phase 6: `flutter_gemma` integration spike

### 6.1 Add package and model loader

Build:

* add `flutter_gemma`;
* load small/dev model or Gemma 4 model;
* verify local inference.

Acceptance:

* app can generate a simple response offline on target device.

### 6.2 Runtime capability check

Build:

* detect supported/unsupported runtime;
* expose Full AI / Lite mode.

Acceptance:

* unsupported devices do not crash.

### 6.3 LocalTutorService wrapper

Build:

* `FlutterGemmaTutorService`;
* basic `generateText` method;
* timeout and error handling.

Acceptance:

* UI never calls `flutter_gemma` directly.

---

## Phase 7: Prompt and JSON response pipeline

### 7.1 Prompt builder

Build:

* global system prompt;
* context packet builder;
* task prompt builder.

Acceptance:

* generated prompt can be inspected in debug mode.

### 7.2 JSON schema validation

Build:

* parse model output;
* validate required fields;
* fallback if invalid.

Acceptance:

* invalid model output does not break UI.

### 7.3 Deterministic fallback templates

Build:

* correction fallback;
* hint fallback;
* role-play fallback.

Acceptance:

* app continues if Gemma times out.

---

## Phase 8: Offline RAG v1

### 8.1 Exact retrieval

Build:

* retrieve current mission;
* target phrase;
* vocabulary;
* grammar focus;
* scenario info.

Acceptance:

* context packet includes correct mission data.

### 8.2 Learner memory retrieval

Build:

* recent errors;
* weak words;
* current mastery.

Acceptance:

* context packet changes by profile.

### 8.3 Generated content cache

Build:

* hash prompt/context;
* cache Gemma output;
* reuse cached output.

Acceptance:

* repeated mission loads faster.

---

## Phase 9: AI coach correction

### 9.1 Coach correction prompt

Build:

* prompt for correction;
* JSON output;
* display correction in coach bubble.

Acceptance:

* learner sentence “This bus go school?” produces correction.

### 9.2 Retry loop

Build:

* ask learner to retry;
* compare retry to target phrase;
* update attempt status.

Acceptance:

* learner can retry and continue.

### 9.3 Mastery event

Build:

* save detected concept;
* result: correct / needs_retry / incorrect.

Acceptance:

* mastery table updates after correction.

---

## Phase 10: AI role-play character

### 10.1 Role-play generation prompt

Build:

* role character stays in scenario;
* no grammar correction;
* short next line.

Acceptance:

* bus conductor produces natural next turn.

### 10.2 Turn manager

Build:

* decide whether coach or role character speaks next.

Acceptance:

* role-play alternates correctly.

### 10.3 Difficulty control

Build:

* easier/same/harder flags;
* app passes selected difficulty.

Acceptance:

* A1 learner gets short lines.

---

## Phase 11: Reading follow-up

### 11.1 Static reading activity

Build:

* display short passage;
* word highlighting;
* simple comprehension question.

Acceptance:

* mission ends with reading activity.

### 11.2 AI-generated reading variant

Build:

* Gemma creates reading passage from transcript;
* cache output.

Acceptance:

* passage uses mission vocabulary.

### 11.3 Reading event logging

Build:

* reading_started;
* question_answered;
* reading_completed.

Acceptance:

* profile reading progress updates.

---

## Phase 12: Writing follow-up

### 12.1 Fill-in-the-blank activity

Build:

* word bank;
* answer checking.

Acceptance:

* learner completes A1 writing item.

### 12.2 Sentence builder

Build:

* drag/tap words into order.

Acceptance:

* “Does this bus go to school?” can be built.

### 12.3 Writing feedback with Gemma

Build:

* prompt for writing feedback;
* corrected sentence;
* retry prompt.

Acceptance:

* “I go market yesterday” returns focused feedback.

---

## Phase 13: Basic PAL / recommendation engine

### 13.1 Mastery score model

Build:

* vocabulary mastery;
* grammar mastery;
* can-do mastery.

Acceptance:

* mission attempts change mastery scores.

### 13.2 Next activity selector

Build rules:

* continue path;
* review weak word;
* remediation;
* next scenario.

Acceptance:

* home screen mission is personalized.

### 13.3 Spaced repetition v1

Build:

* `next_review_at`;
* review queue.

Acceptance:

* weak words reappear after due interval.

---

## Phase 14: Gamification v1

### 14.1 Stars and badges

Build:

* mission completion stars;
* first conversation badge;
* retry success badge.

Acceptance:

* learner receives reward after mission.

### 14.2 Daily speaking goal

Build:

* track active speaking/practice minutes;
* daily progress.

Acceptance:

* home screen goal updates.

### 14.3 Streaks

Build:

* local streak count;
* reset/preserve rules.

Acceptance:

* streak updates per profile.

Chimple’s technical requirements already identify gamification as a configurable component for rewards, progress tracking, goals, and analytics; use a lightweight local version first. 

---

## Phase 15: Speech integration v1

### 15.1 Audio recording

Build:

* mic permission;
* record response;
* playback.

Acceptance:

* learner audio is captured locally.

### 15.2 Text input fallback

Build:

* learner can type response if STT unavailable.

Acceptance:

* role-play works without STT.

### 15.3 Offline STT spike

Build:

* evaluate offline STT model/plugin;
* convert audio to transcript.

Acceptance:

* acceptable accuracy for short A1 phrases.

---

## Phase 16: Speech integration v2

### 16.1 TTS integration

Build:

* offline TTS for coach and role character.

Acceptance:

* AI lines are spoken offline.

### 16.2 Pronunciation v1

Build:

* compare transcript to target phrase;
* detect missing words;
* rough fluency timing.

Acceptance:

* learner gets simple “try this word again” feedback.

### 16.3 Voice-first mission mode

Build:

* mic-driven flow;
* no typing required for guided role-play.

Acceptance:

* 5-minute spoken mission works offline.

---

## Phase 17: Safety and guardrails

### 17.1 Output safety validation

Build:

* schema validation;
* topic whitelist;
* personal-data detection;
* fallback response.

Acceptance:

* unsafe or malformed output is not shown.

### 17.2 Input redirection

Build:

* learner off-topic input redirects to mission.

Acceptance:

* app handles irrelevant learner input safely.

### 17.3 Camera safety rules

Build:

* do not identify people;
* no face/person inference;
* only safe object/scene labels.

Acceptance:

* camera mode avoids sensitive descriptions.

---

## Phase 18: Camera talk v1

### 18.1 Camera capture

Build:

* take picture;
* save temporarily;
* delete after session unless explicitly stored.

Acceptance:

* learner can start picture-talk mode.

### 18.2 Manual object labels

Build MVP without vision first:

* learner/teacher chooses object labels;
* AI creates conversation.

Acceptance:

* picture talk works with selected labels.

### 18.3 Vision model spike

Build:

* local object detector or Gemma multimodal if feasible;
* safe object list.

Acceptance:

* app detects simple objects offline.

---

## Phase 19: Content authoring pipeline v1

### 19.1 Content pack builder script

Build:

* source JSON/CSV → content pack;
* validation;
* versioning.

Acceptance:

* curriculum team can create a new pack.

### 19.2 Human-reviewed generated content

Build:

* generate reading/writing/dialogue variants;
* mark approved;
* pack into app.

Acceptance:

* generated content is reusable offline.

### 19.3 Content QA checklist

Build checklist for:

* level appropriateness;
* safety;
* cultural relevance;
* translation quality;
* grammar accuracy.

Chimple’s GenAI Studio workflow already follows a pattern where learning material plus grade/language/outcome/cultural context is processed, LLMs suggest learning points, educators approve/edit, and outputs are published after safety checks. 

---

## Phase 20: Teacher/parent visibility v1

### 20.1 Local progress report

Build:

* per-profile summary;
* words practiced;
* can-dos completed;
* weak concepts.

Acceptance:

* teacher/parent can view progress on device.

### 20.2 Export report

Build:

* export local report as JSON/PDF/CSV.

Acceptance:

* teacher can extract progress without internet.

### 20.3 Assignment placeholder

Build:

* local teacher assignment file import.

Acceptance:

* app can load assigned mission pack.

---

## Phase 21: Internal pilot build

### 21.1 Pilot content pack

Build:

* 20 missions;
* 5 scenarios;
* Pre-A1 to A1.2;
* one home language.

Acceptance:

* complete learning path for 2–3 weeks of use.

### 21.2 Device performance test

Test:

* cold start;
* model load time;
* generation latency;
* crash rate;
* storage size;
* battery impact.

Acceptance:

* full AI mode works on target devices;
* fallback works on unsupported devices.

### 21.3 Usability test

Test with 5–10 learners.

Observe:

* home screen comprehension;
* two-character flow;
* mic usage;
* correction tolerance;
* reading/writing difficulty.

Acceptance:

* top 10 usability issues documented.

---

## Phase 22: Iteration after pilot 1

### 22.1 Simplify confusing flows

Focus:

* fewer buttons;
* clearer coach prompts;
* easier retry loop.

### 22.2 Tune prompts

Focus:

* shorter corrections;
* less over-explaining;
* better home-language support;
* stricter JSON.

### 22.3 Improve content sequencing

Focus:

* missions too easy/hard;
* scenario order;
* vocabulary repetition.

---

## Phase 23: Expanded content and CEFR coverage

### 23.1 Add A2.1 missions

Build:

* directions;
* doctor;
* teacher conversation;
* daily routine;
* yesterday events.

### 23.2 Add grammar remediation library

Build:

* plurals;
* articles;
* am/is/are;
* does questions;
* simple past;
* prepositions.

### 23.3 Add vocabulary review games

Build:

* listen and choose;
* match word/picture;
* speak the word;
* use in sentence.

---

## Phase 24: Evidence and experimentation readiness

### 24.1 Local experiment flags

Build:

* feature flags;
* A/B assignment from local config;
* offline event logging.

Acceptance:

* app can compare correction styles offline.

Chimple’s technical requirements for experimentation note that clients should download rules, operate offline, and sync data once connectivity is available. 

### 24.2 Metrics dashboard export

Build:

* completion;
* engagement;
* mastery;
* latency;
* errors.

### 24.3 Pilot protocol

Define:

* baseline;
* 2-week usage;
* post-assessment;
* interview guide.

---

## Phase 25: School pilot build

### 25.1 Harden install/provisioning

Build:

* model/content preloading;
* no network dependency;
* device reset/recovery;
* profile backup/export.

### 25.2 Classroom mode

Build:

* multiple learners per tablet;
* quick profile switch;
* session timeout;
* teacher PIN for reports.

### 25.3 Pilot support tools

Build:

* debug screen;
* content version display;
* model version display;
* export logs.

---

# 12. Suggested release milestones

## Milestone A: Clickable prototype

Includes:

* home;
* profile;
* canned role-play;
* reading/writing mock;
* no AI.

## Milestone B: Local AI prototype

Includes:

* `flutter_gemma`;
* prompt builder;
* role-play/correction;
* JSON validation;
* one scenario.

## Milestone C: Learning MVP

Includes:

* 5 scenarios;
* 20 missions;
* multi-profile;
* local RAG;
* mastery tracking;
* reading/writing;
* fallback templates.

## Milestone D: Voice MVP

Includes:

* offline TTS;
* recording;
* STT or transcript fallback;
* pronunciation v1.

## Milestone E: Pilot MVP

Includes:

* 20–30 missions;
* stable content pack;
* local reports;
* safety guardrails;
* export logs;
* tested on target devices.

## Milestone F: Classroom pilot

Includes:

* teacher assignment import;
* profile switching at scale;
* local reports;
* school deployment workflow.

---

# 13. What to build first

The first engineering slice should be:

1. Flutter shell
2. Multi-profile local DB
3. Static mission player
4. One CEFR content pack
5. `flutter_gemma` integration spike
6. Prompt → JSON → UI loop
7. Coach correction in one scenario
8. Local mastery update

That slice proves the hardest parts: **offline model, learning context, two-character interaction, and local learner memory.**

---

# 14. Final product definition

**VocaLoca is an offline-first Flutter app that uses local Gemma 4 through `flutter_gemma` to power a two-character AI language tutor. The app stores CEFR-aligned curriculum, local learner profiles, RAG indexes, approved content packs, and mastery data on-device. Gemma generates natural role-play, corrections, hints, and reading/writing follow-ups, while the app controls progression, assessment, safety, and evidence of learning.**

[1]: https://pub.dev/packages/flutter_gemma "flutter_gemma | Flutter package"
