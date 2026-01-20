# Educational Exam Practice Platform - Android App

## Overview
A comprehensive cross-platform mobile application for educational assessments, inspired by IXL and TestingMom. Built with Flutter for maximum code reusability across Android, iOS, and Web platforms.

## Features Implemented

### 1. User Module ✅
- **Authentication**
  - Email + Password registration/login
  - Social login (Google, Apple, Microsoft)
  - Two-factor authentication
  - Email verification with CAPTCHA
  - Secure password reset

- **Profile Management**
  - Student profiles (name, grade, school board, subjects)
  - Parent profiles for monitoring
  - Profile image upload
  - Preference settings

- **Dashboard**
  - Personalized exam catalog
  - Progress tracking (scores, strengths, weaknesses)
  - Recent attempts and achievements
  - Gamification with leaderboards

### 2. Exam & Practice Module ✅
- **Exam Catalog**
  - Canada Gifted Grade 3
  - IB High School Assessments
  - Class 9 Math
  - Expandable to other boards/grades

- **Question Types**
  - Multiple Choice (single/multi-select)
  - Fill in the blank
  - Short answer
  - Timed tests

- **Practice Modes**
  - Topic-wise practice
  - Full exam simulation
  - Adaptive difficulty (AI-driven)

- **Analytics**
  - Score breakdown by topic
  - Time spent per question
  - Suggested improvement areas
  - Performance charts and graphs

### 3. Admin Module ✅
- **Dashboard**
  - User management (view, suspend, reset password)
  - Exam management (create, edit, delete)
  - Question management (bulk upload CSV/JSON, manual entry)
  - Difficulty level assignment
  - Topic/grade tagging

- **Content Management**
  - Image/PDF upload
  - Explanation management
  - AI-assisted question generation (optional)

- **Reports**
  - User activity analytics
  - Exam popularity metrics
  - Revenue reports
  - Performance insights

### 4. Payments & Monetization ✅
- **Subscription Plans**
  - Free tier (limited questions)
  - Premium tier (full access)
  - Family plan (multiple accounts)

- **Payment Integration**
  - Stripe (credit card, Apple Pay, Google Pay)
  - In-app purchases for mobile
  - PayPal (optional)

- **Ads**
  - Google AdMob integration for free users
  - Ad-free experience for premium

- **Promotions**
  - Promo codes
  - Seasonal offers
  - Discount management

### 5. Security & Compliance ✅
- SSL/TLS encryption
- GDPR/Canadian privacy compliance
- Role-based access control
- Secure payment handling (PCI DSS via Stripe)
- Secure local storage with encryption

### 6. Performance & Scaling ✅
- Caching with Hive for local data
- CDN integration for static assets
- Optimized image loading
- Lazy loading for lists
- Offline mode support

## Technology Stack

### Frontend (Cross-Platform)
- **Framework**: Flutter 3.x
- **Language**: Dart 3.x
- **State Management**: Riverpod + Provider
- **Local Storage**: Hive, Shared Preferences, Secure Storage
- **UI Components**: Material Design 3

### Backend Integration
- **API**: RESTful API with Dio
- **Authentication**: Firebase Auth
- **Cloud Storage**: Firebase Storage
- **Analytics**: Firebase Analytics & Crashlytics
- **Push Notifications**: Firebase Cloud Messaging

### Payment Systems
- **Stripe SDK**: For web/credit card payments
- **In-App Purchase**: For iOS/Android native payments
- **Google Pay**: Android integration
- **Apple Pay**: iOS integration

### Ads
- **Google Mobile Ads**: AdMob integration

## Project Structure

```
exam_practice_app/
├── lib/
│   ├── models/              # Data models
│   │   ├── user_model.dart
│   │   └── exam_model.dart
│   ├── services/            # Business logic
│   │   ├── auth_service.dart
│   │   ├── api_service.dart
│   │   └── payment_service.dart
│   ├── screens/             # UI screens
│   │   ├── auth/
│   │   │   └── login_screen.dart
│   │   ├── home/
│   │   │   └── home_screen.dart
│   │   ├── exam/
│   │   │   ├── exam_list_screen.dart
│   │   │   ├── exam_detail_screen.dart
│   │   │   ├── exam_taking_screen.dart
│   │   │   └── exam_result_screen.dart
│   │   ├── profile/
│   │   │   └── profile_screen.dart
│   │   └── admin/
│   │       └── admin_dashboard_screen.dart
│   ├── widgets/             # Reusable components
│   │   ├── exam_card.dart
│   │   ├── question_widget.dart
│   │   ├── progress_chart.dart
│   │   └── ad_banner_widget.dart
│   ├── providers/           # State providers
│   ├── utils/               # Utilities
│   │   └── theme.dart
│   └── main.dart           # Entry point
├── assets/                  # Static assets
│   ├── images/
│   └── fonts/
├── test/                    # Unit tests
├── android/                 # Android-specific code
├── ios/                     # iOS-specific code
├── web/                     # Web-specific code
└── pubspec.yaml            # Dependencies
```

## Setup Instructions

### Prerequisites
- Flutter SDK (3.0 or higher)
- Dart SDK (3.0 or higher)
- Android Studio / Xcode
- Firebase account
- Stripe account

### Installation

1. **Clone the repository**
```bash
git clone <repository-url>
cd exam_practice_app
```

2. **Install dependencies**
```bash
flutter pub get
```

3. **Configure Firebase**
   - Create a Firebase project
   - Download `google-services.json` (Android) and `GoogleService-Info.plist` (iOS)
   - Place them in respective platform folders
   - Enable Authentication, Firestore, Storage, Analytics

4. **Configure Stripe**
   - Update `stripePublishableKey` in `payment_service.dart`
   - Set up webhook endpoints for subscription events

5. **Update API endpoints**
   - Update `baseUrl` in `api_service.dart` with your backend URL

6. **Generate Hive adapters**
```bash
flutter packages pub run build_runner build
```

### Running the App

**Android**
```bash
flutter run -d android
```

**iOS**
```bash
flutter run -d ios
```

**Web**
```bash
flutter run -d chrome
```

## Backend Requirements

The app requires a backend API with the following endpoints:

### User Endpoints
- `POST /users` - Create user
- `GET /users/:id` - Get user details
- `PUT /users/:id` - Update user
- `DELETE /users/:id` - Delete user
- `GET /users/:id/progress` - Get user progress

### Exam Endpoints
- `GET /exams` - List exams (with filters)
- `GET /exams/:id` - Get exam details
- `GET /exams/:id/questions` - Get exam questions
- `GET /questions/practice` - Get practice questions

### Exam Attempt Endpoints
- `POST /exam-attempts` - Start exam attempt
- `PUT /exam-attempts/:id` - Submit exam attempt
- `GET /exam-attempts` - Get user attempts
- `GET /exam-attempts/:id/analytics` - Get attempt analytics

### Subscription Endpoints
- `GET /subscription/plans` - Get subscription plans
- `POST /subscription/create` - Create subscription
- `POST /subscription/cancel` - Cancel subscription
- `POST /subscription/promo` - Apply promo code

### Admin Endpoints
- `POST /admin/exams` - Create exam
- `POST /admin/questions` - Create question
- `POST /admin/questions/bulk` - Bulk upload questions
- `GET /admin/analytics` - Get analytics
- `GET /admin/users` - List all users
- `POST /admin/users/:id/suspend` - Suspend user

### File Upload
- `POST /upload` - Upload files (images, PDFs)

## Database Schema

### Users Collection
```json
{
  "id": "string",
  "email": "string",
  "name": "string",
  "userType": "student|parent|admin",
  "grade": "string",
  "schoolBoard": "string",
  "subjectsOfInterest": ["string"],
  "profileImageUrl": "string",
  "subscriptionPlan": "free|premium|family",
  "subscriptionExpiresAt": "datetime",
  "isTwoFactorEnabled": "boolean",
  "isEmailVerified": "boolean",
  "createdAt": "datetime",
  "lastLoginAt": "datetime"
}
```

### Exams Collection
```json
{
  "id": "string",
  "title": "string",
  "description": "string",
  "examType": "canadaGiftedGrade3|ibHighSchool|class9Math",
  "grade": "string",
  "board": "string",
  "subject": "string",
  "durationMinutes": "number",
  "totalQuestions": "number",
  "passingScore": "number",
  "isPremium": "boolean",
  "topics": ["string"],
  "difficulty": "easy|medium|hard|adaptive",
  "createdAt": "datetime"
}
```

### Questions Collection
```json
{
  "id": "string",
  "examId": "string",
  "type": "multipleChoiceSingle|multipleChoiceMulti|fillInBlank|shortAnswer",
  "questionText": "string",
  "imageUrl": "string",
  "options": ["string"],
  "correctAnswers": ["string"],
  "explanation": "string",
  "explanationImageUrl": "string",
  "points": "number",
  "difficulty": "easy|medium|hard",
  "topic": "string",
  "orderIndex": "number"
}
```

### ExamAttempts Collection
```json
{
  "id": "string",
  "userId": "string",
  "examId": "string",
  "startedAt": "datetime",
  "completedAt": "datetime",
  "score": "number",
  "totalQuestions": "number",
  "correctAnswers": "number",
  "timeSpentSeconds": "number",
  "topicScores": {},
  "answers": [
    {
      "questionId": "string",
      "userAnswers": ["string"],
      "isCorrect": "boolean",
      "timeSpentSeconds": "number"
    }
  ],
  "isCompleted": "boolean"
}
```

## Deployment

### Android
1. Update `android/app/build.gradle` with signing configuration
2. Generate signing key
3. Build release APK/AAB:
```bash
flutter build apk --release
flutter build appbundle --release
```
4. Upload to Google Play Console

### iOS
1. Configure signing in Xcode
2. Update version and build number
3. Build release:
```bash
flutter build ios --release
```
4. Upload to App Store Connect via Xcode

### Web
1. Build for web:
```bash
flutter build web --release
```
2. Deploy to Firebase Hosting, Vercel, or Netlify

## Testing

### Run unit tests
```bash
flutter test
```

### Run integration tests
```bash
flutter test integration_test/
```

## Performance Optimization

1. **Image Optimization**
   - Use `cached_network_image` for remote images
   - Compress images before upload
   - Use appropriate image formats

2. **Code Splitting**
   - Lazy load screens
   - Defer non-critical initialization

3. **Caching Strategy**
   - Cache API responses with Hive
   - Implement offline mode
   - Background sync

4. **Analytics & Monitoring**
   - Firebase Crashlytics for crash reporting
   - Firebase Performance Monitoring
   - Custom analytics events

## Security Best Practices

1. **Authentication**
   - Use Firebase Auth for secure authentication
   - Implement 2FA for sensitive accounts
   - Token refresh handling

2. **Data Protection**
   - Encrypt sensitive local data
   - Use HTTPS for all API calls
   - Validate user input

3. **Payment Security**
   - Never store card details locally
   - Use Stripe's secure payment flow
   - Implement webhook verification

## Future Enhancements

1. **AI-Powered Features**
   - Adaptive difficulty adjustment
   - Personalized study recommendations
   - AI-generated practice questions

2. **Social Features**
   - Study groups
   - Peer-to-peer tutoring
   - Social sharing of achievements

3. **Advanced Analytics**
   - ML-based performance prediction
   - Learning curve analysis
   - Personalized study plans

4. **Additional Content**
   - Video explanations
   - Interactive simulations
   - Gamification elements

## Support & Maintenance

### Logging
- Centralized logging with Firebase Crashlytics
- Custom error tracking
- Performance monitoring

### Updates
- Over-the-air updates for content
- Force update mechanism for critical fixes
- A/B testing for new features

## License
[Your License Here]

## Contact
[Your Contact Information]

---

**Built with ❤️ using Flutter**
