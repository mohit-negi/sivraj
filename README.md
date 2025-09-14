# Sivraj - Local-First Multimodal Android Agent

- Sivarj a mirror of jarvis

> **A privacy-preserving, local-first Android agent that helps you start your day with intelligent planning, voice guidance, and actionable insights.**

## ��� Project Vision

Sivraj is designed as a **local-first, privacy-preserving multimodal Android agent** that transorms your morning routine through intelligent task planning, voice-guided summaries, and micro-action nudges. The MVP focuses on core workflow automation while maintaining a modular architecture for future multimodal capabilities.

### Core Philosophy
- **Privacy First**: All data processing happens locally on your device
- **Local-First**: No cloud dependency for core functionality
- **Modular Design**: Extensible architecture for future capabilities
- **User-Centric**: Designed around natural morning workflows

## ���️ Architecture Overview

### MVP Architecture
```
React Native UI  <---- native bridge ---->  Native Android Layer
(MorningSummary, Timeline)                 (AgentService, Room, TTS)
        ^                                             |
        |                                             v
        +----------------------------------> AlarmManager / WorkManager
```

### Future Target Architecture
- **Device Stack**: RN UI, AgentService, on-device runtime (TFLite/ONNX/llama.cpp), Vector DB, Skill SDK, local metrics
- **Optional Cloud**: reasoning LLM, encrypted backup, model CI/CD, aggregated telemetry (opt-in only)

## ��� MVP Features

### Core Workflow
1. **Smart Alarm**: AgentService triggered by AlarmManager
2. **Intelligent Planning**: Greedy deterministic planner creates daily schedule
3. **Voice Summary**: TTS delivers morning plan highlights
4. **Actionable UI**: React Native interface for timeline and task management
5. **Micro-Actions**: Contextual nudges for breaks, hydration, posture

### Key Components
- **Native AgentService** (Kotlin): Core planning and orchestration
- **React Native UI**: Morning Summary & Timeline screens
- **AgentBridge**: Native ↔ JS communication layer
- **Room Database**: Local storage for tasks, metrics, calendar mirror
- **Planner Engine**: Greedy deterministic scheduling algorithm
- **TTS Integration**: Voice-guided morning summaries

## ��� Data Schemas

### Task Schema
```json
{
  "id": "uuid",
  "title": "string",
  "duration_min": 45,
  "earliest": "ISO8601|null",
  "deadline": "ISO8601|null",
  "priority": 1,
  "tags": ["work"],
  "location": "string|null",
  "min_chunk": 25,
  "completed": false,
  "created_at": "ISO8601"
}
```

### Plan Schema
```json
{
  "date": "YYYY-MM-DD",
  "wake_time": "ISO8601",
  "slots": [
    {
      "slot_id": "uuid",
      "type": "task|calendar|micro",
      "task_id": "uuid|null",
      "title": "string",
      "start": "ISO8601",
      "end": "ISO8601",
      "meta": { "priority": 7, "notes": "" }
    }
  ],
  "summary": {
    "total_tasks": 5,
    "focus_blocks": 2,
    "micro_actions": 4
  },
  "version": 1
}
```

## ��� API Contract

### JavaScript → Native (Promises)
- `startAgent(opts)` - Initialize the agent service
- `getPlan(date?)` - Retrieve daily plan
- `createTask(taskJson)` - Add new task
- `updateTask(id, patch)` - Modify existing task
- `deleteTask(id)` - Remove task
- `reportMicroAction(slot_id, status)` - Report micro-action completion
- `snoozePlan(minutes)` - Delay plan execution

### Native → JavaScript (Events)
- `onPlanReady(planJson)` - Plan generation complete
- `onNotificationAction({action, meta})` - User interaction with notifications
- `onASRResult({text, confidence})` - Voice input results (future)
- `onPoseConfirmed({slot_id, confidence})` - Gesture confirmations (future)

## ��� Planning Algorithm

### Greedy Deterministic Planner
The core planning engine uses a greedy approach with intelligent scoring:

**Inputs**: Tasks array, busy intervals, day window
**Scoring**: Priority and deadline urgency weighted
**Features**:
- Computes free time slots
- Schedules tasks by priority score
- Chunks large tasks into manageable blocks
- Respects deadlines and calendar events
- Injects micro-actions for breaks
- Returns conflict metadata for unresolved items

## ��� User Experience

### Morning Flow
1. **Alarm Triggers**: AgentService activates at scheduled time
2. **Voice Summary**: TTS delivers daily plan highlights
3. **Notification**: Accept | Edit | Snooze options
4. **Morning Summary**: Timeline view with task breakdown
5. **Micro-Actions**: Contextual reminders throughout the day

### Future UX Enhancements
- Voice-first interactions (ASR + NLU)
- Gesture/pose confirmations
- Personalized motivational nudges
- AR/vision support for contextual reminders

## ���️ Development Setup

### Prerequisites
- Node.js 18+
- Android Studio
- React Native development environment
- Expo CLI

### Installation
```bash
# Clone the repository
git clone <repository-url>
cd sivraj

# Install dependencies
npm install

# Start the development server
npm start

# Run on Android
npm run android

# Run on iOS
npm run ios
```

### Project Structure
```
sivraj/
├── android/                 # Native Android code
│   ├── app/                # AgentService, Room DB, TTS
│   └── gradle/             # Build configuration
├── ios/                    # iOS configuration
├── src/                    # React Native source
│   ├── screens/           # MorningSummary, Timeline
│   ├── components/        # Reusable UI components
│   └── services/          # AgentBridge integration
├── docs/                   # Documentation
│   ├── agent-protocol.md  # API specification
│   └── planner-tests.md   # Algorithm documentation
└── tests/                  # Test suites
    ├── unit/              # Planner, DB, bridge tests
    └── integration/       # E2E workflow tests
```

## ��� Development Roadmap

### High Priority (MVP)
- [ ] **NP-001**: Scaffold repository structure
- [ ] **NP-002**: AgentService skeleton (Kotlin)
- [ ] **NP-003**: Planner.js + unit tests
- [ ] **NP-004**: AgentBridge implementation
- [ ] **NP-005**: MorningSummary React Native screen
- [ ] **NP-006**: Alarm system integration
- [ ] **NP-007**: Room DB + Task CRUD + Calendar mirror
- [ ] **NP-008**: Planner integration
- [ ] **NP-009**: Timeline UI & reschedule functionality

### Medium Priority
- [ ] **NP-010**: Micro-actions injection system
- [ ] **NP-011**: Permissions & onboarding flow
- [ ] **NP-012**: Unit & E2E test coverage
- [ ] **NP-013**: Local metrics dashboard

### Future Enhancements
- [ ] **NP-020**: ASR/Wakeword specification
- [ ] **NP-021**: Pose detection integration
- [ ] **NP-022**: NLU baseline implementation
- [ ] **NP-023**: Cloud reasoning (opt-in)
- [ ] **NP-024**: Cross-device sync
- [ ] **NP-025**: Wearable integration

## ✅ MVP Acceptance Criteria

- [ ] Alarm triggers AgentService successfully
- [ ] Plan JSON generated from tasks + calendar data
- [ ] TTS delivers morning summary
- [ ] Actionable notifications appear
- [ ] React Native Morning Summary renders plan
- [ ] Task CRUD operations work with Room DB
- [ ] Planner unit tests pass
- [ ] All data remains local (privacy preserved)

## ��� Testing Strategy

### Unit Tests
- Planner algorithm correctness
- Database operations
- Bridge communication mocks

### Integration Tests
- Alarm → Service → UI workflow
- End-to-end user journeys

### Manual QA
- TTS quality and timing
- Notification flow accuracy
- Rescheduling persistence
- Battery optimization compatibility

## ��� Security & Privacy

### Privacy-First Design
- **Local Storage**: All data stored on device by default
- **Encrypted Database**: Room DB encrypted at rest
- **Opt-in Cloud**: Future cloud features require explicit consent
- **E2E Encryption**: Any cloud sync will be end-to-end encrypted
- **Data Export**: Full data export and deletion capabilities

### Security Measures
- Biometric confirmation for sensitive actions
- Secure communication channels
- Regular security audits
- Compliance with privacy regulations

## ��� Performance Considerations

### Optimization Strategy
- Lightweight deterministic planner
- Deferred ML processing (optional/quantized models)
- NNAPI/GPU delegates for future ML workloads
- Foreground service only for alarm management
- Efficient battery usage patterns

## ��� Getting Started

1. **Setup Development Environment**
   ```bash
   npm install
   npm start
   ```

2. **Run on Device**
   ```bash
   npm run android  # or npm run ios
   ```

3. **Run Tests**
   ```bash
   npm test
   ```

4. **Build for Production**
   ```bash
   npm run build:android
   ```

## ��� Contributing

We welcome contributions! Please see our [Contributing Guidelines](CONTRIBUTING.md) for details on:
- Code style and standards
- Testing requirements
- Pull request process
- Issue reporting

## �� License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## ��� Support

- **Documentation**: Check the `docs/` directory for detailed specifications
- **Issues**: Report bugs and feature requests via GitHub Issues
- **Discussions**: Join our community discussions for questions and ideas

## ��� Future Vision

Sivraj is designed to evolve into a comprehensive personal AI assistant that:
- Understands your habits and preferences
- Provides contextual, proactive assistance
- Maintains complete privacy and local control
- Integrates seamlessly with your digital ecosystem
- Adapts and learns from your feedback

---

**Built with ❤️ for privacy-conscious users who want intelligent assistance without compromising their data.**
