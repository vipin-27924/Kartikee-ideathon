The Problem

Therapy and support sessions can contain changes in body posture, movement, face/head visibility, environmental distractions, and camera conditions. Manually reviewing an entire session can be time-consuming and may make consistent measurement difficult.
The project addresses:
How can camera-based AI convert session video into observable, measurable information and useful session summaries without making unsupported diagnostic or psychological claims?
Who Experiences This Problem?
Therapists and caregivers conducting structured sessions.
Children participating in camera-based therapy or support activities.
Researchers studying observable movement and interaction patterns.
Developers building assistive camera-based applications.

--> Why Is It a Problem?

Raw video contains a large amount of information:

Live Camera
    ↓
Many Frames
    ↓
Manual Observation
    ↓
Difficult to Track Everything
    ↓
Limited Structured Session Data

The system therefore focuses on converting visual input into measurable session information.

2. Existing Solutions

Manual Observation
Therapists can observe and record session events.
Limitation: Manual observation can be time-consuming and may not capture every measurable visual event consistently.
Basic Camera Recording
A conventional camera can record a session for later review.
Limitation: Recording provides video but does not automatically produce structured measurements.
Computer Vision
Computer-vision models can detect faces, body landmarks, people, objects, and movement.
Limitation: Individual models normally provide narrow outputs and do not automatically create a complete session-level summary.
Multimodal AI
A multimodal model such as MedGemma can combine image and text information for higher-level summarization.
Limitation: It should not replace dedicated real-time detection models or be treated as an autonomous diagnostic system.
Identified Gap
The proposed system combines:
Real-time computer vision + session-level metrics + multimodal session summarization.

3. Proposed Solution

The system processes live camera frames through a computer-vision pipeline and converts the results into session-level observations.

Android CameraX
      ↓
Frame Sampling
      ↓
Computer Vision
      ↓
Face / Pose / Object / Movement Analysis
      ↓
Session Metrics
      ↓
MedGemma Session Summary
      ↓
FastAPI
      ↓
Android Application

The system focuses on observable information rather than diagnosing autism or inferring internal states.

Example measurements:

{
  "session_duration": 600,
  "face_visible_percentage": 91.4,
  "body_visible_percentage": 87.2,
  "movement_percentage": 34.6,
  "posture_changes": 17,
  "additional_people_detected": 2,
  "camera_quality_percentage": 94.1
}

These measurements should not automatically be interpreted as:

"child is autistic"
"child is distracted"
"child is not listening"

4. Key Features

--> Face Detection
Detects whether a face is visible and can provide observable face/head-orientation features.
Possible measurements:
Face visibility percentage.
Face detection frequency.
Head orientation estimates.
Periods when the face is not visible.

--> Body Pose Analysis
Uses pose landmarks to measure observable posture and movement.
Possible measurements:
Body visibility.
Pose landmarks.
Posture changes.
Body position changes.
Movement patterns.

--> Movement Analysis
Analyzes changes between frames to estimate movement over time.
Frame N
   +
Frame N+1
   ↓
Motion Features
   ↓
Session Movement Metrics

Movement measurements are observations and should not automatically be treated as evidence of hyperactivity or a clinical condition.

--> Environmental Object Detection

Uses object detection to identify people and relevant environmental objects.
Possible measurements:
Additional people.
Detected objects.
Environmental changes.
Events requiring human review.

--> Camera Quality

Identifies conditions that may affect visual analysis.
Possible factors:
Blur.
Visibility.
Lighting.
Occlusion.
Image quality.

--> Session Metrics
Frame-level outputs are aggregated into session-level measurements.

--> MedGemma Session Summary
MedGemma 1.5 4B can act as a higher-level multimodal summarization layer:

Session Metrics
      +
Selected Representative Frame
      +
Structured Prompt
      ↓
MedGemma
      ↓
Therapist-Friendly Summary

It should summarize observations and limitations, not diagnose the child.

--> FastAPI Integration
The AI service provides:
GET  /health
POST /analyze/frame
POST /analyze/video
POST /ai/session-summary

5. Technical Approach

System Overview

                         USER
                           │
                           ▼
                  ┌─────────────────┐
                  │ Android CameraX │
                  └────────┬────────┘
                           │
                           ▼
                  ┌─────────────────┐
                  │ FastAPI Backend │
                  │      :8000      │
                  └────────┬────────┘
                           │
                           ▼
                  ┌─────────────────┐
                  │   AI Service    │
                  │      :8001      │
                  └────────┬────────┘
                           │
            ┌──────────────┼──────────────┐
            ▼              ▼              ▼
        MediaPipe         YOLO       Camera Quality
        Face / Pose      Objects        Analysis
            │              │              │
            └──────────────┼──────────────┘
                           ▼
                    Session Metrics
                           │
                           ▼
                       MedGemma
                           │
                           ▼
                    Session Summary

Project Structure

ai/
├── app/
│   ├── main.py
│   ├── pipeline.py
│   ├── pose.py
│   ├── face.py
│   ├── objects.py
│   ├── movement.py
│   ├── camera_quality.py
│   └── medgemma.py
├── requirements.txt
├── Dockerfile
└── README.md

Real-Time Processing

Android CameraX should not send every camera frame to the server.

A starting point is:

CameraX
   ↓
Frame Sampling
   ↓
2–5 FPS initially
   ↓
AI Analysis

Computer-Vision Layer

Frame
 ↓
Face Detection
 ↓
Pose Detection
 ↓
Object Detection
 ↓
Movement Features
 ↓
Camera Quality
 ↓
Session Metrics

MedGemma Layer

MedGemma is positioned after the CV pipeline:

Many Camera Frames
       ↓
Real-Time CV
       ↓
Compact Session Metrics
       +
Selected Visual Evidence
       ↓
MedGemma
       ↓
Natural-Language Summary

MedGemma should not be called for every live frame.

6. Technology Stack
Layer
Technology
Purpose
Mobile
Android / Kotlin
Camera application
Camera
CameraX
Live camera capture
Runtime
Python 3.12
AI service runtime
API
FastAPI
AI/API layer
Server
Uvicorn
ASGI server
Pose
MediaPipe
Body landmark analysis
Face
MediaPipe
Face detection/features
Objects
YOLO
Person/object detection
Image Processing
Pillow / image processing tools
Image handling
Multimodal AI
MedGemma 1.5 4B
Session summarization
ML Runtime
PyTorch / Transformers
MedGemma inference
Containerization
Docker
Deployment
Backend
Existing FastAPI backend
Application/session integration
Database
Existing project database
Session and metric storage

The design follows:
Problem
   ↓
Observable Requirement
   ↓
AI Component
   ↓
Session Metric
   ↓
Application Feature

7. Expected Impact
Therapists and Caregivers
The system can provide structured session observations that help with session review and progress monitoring.
Children
The goal is to support observation and assistance without labeling or diagnosing a child from camera data.
Researchers
The system can provide structured measurements for longitudinal analysis:

Session 1
   ↓
Session 2
   ↓
Session 3
   ↓
Personal Trend

The emphasis should be on changes relative to the individual's own previous sessions.
Developers
The modular architecture allows individual AI components to be improved independently.

Improve Pose Model
       ↓
Pose Module

Improve Object Model
       ↓
Object Module

Improve Temporal Model
       ↓
Session Analysis

Improve Summary Generation
       ↓
MedGemma Layer

Expected Value

The key potential value is:
Converting continuous camera input into measurable, reviewable session information.

8. Future Scope

Temporal Behavior Modeling
Future versions can add temporal models such as:
LSTM
GRU
Temporal CNN
Transformer-based temporal models
Frame 1
Frame 2
Frame 3
...
Frame N
   ↓
Temporal Model
   ↓
Session Pattern

Personalized Baselines
Future versions can compare a session with the same individual's previous sessions.
Current Session
      +
Previous Sessions
      ↓
Personal Baseline
      ↓
Change / Trend

On-Device AI

Lightweight CV components could eventually run directly on Android.

Android
 ├── CameraX
 ├── Face
 ├── Pose
 └── Lightweight Detection
          │
          ▼
      Fast Feedback

Server
 ├── Session Aggregation
 └── MedGemma

This can reduce latency, network usage, and transmission of raw video.

Therapist Review

A future interface could provide:

Session Summary
      ↓
Timeline
      ↓
Detected Events
      ↓
Representative Frames
      ↓
Session Metrics
      ↓
Previous Session Comparison

Privacy and Security

Because the system may process children's camera data, future deployments should include:

Data minimization.

Secure transmission.

Encryption.
Authentication and authorization.
Defined retention policies.
Appropriate consent.
Audit logging.
Ethical and clinical review.

Clinical Validation
Before clinical use, the system should undergo appropriate validation and independent evaluation.
It should not be presented as an autonomous diagnostic system.

Conclusion
This project demonstrates a layered approach to camera-based AI for therapy and support applications.

Instead of asking:
"Can an AI camera diagnose or judge a child?"

the system asks:
"What observable information can be measured reliably from the camera, and how can that information support human review?"

The resulting model is:

                 LIVE CAMERA
                      │
                      ▼
                   CameraX
                      │
                      ▼
              Computer Vision
          ┌───────────┼───────────┐
          ▼           ▼           ▼
        Face         Pose       Objects
          │           │           │
          └───────────┼───────────┘
                      ▼
                  Movement
                      │
                      ▼
               Camera Quality
                      │
                      ▼
               Session Metrics
                      │
                      ▼
                  MedGemma
                      │
                      ▼
              Session Summary
                      │
                      ▼
                Human Review
