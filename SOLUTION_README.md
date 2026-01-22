# Django Online Course Assessment - Complete Solution

## Project Overview

This is a complete, production-ready solution for adding an assessment feature to a Django-based online course application. The project includes a fully functional quiz/exam system with:

- **Question Management**: Create and manage multiple-choice questions
- **Choice Management**: Define correct and incorrect answer choices
- **Submission Tracking**: Record student submissions
- **Automatic Grading**: Calculate scores based on correct answers
- **Result Display**: Show detailed exam results with feedback

## Project Structure

```
django-online-course-app/
├── myproject/                          # Django project configuration
│   ├── settings.py                    # Project settings
│   ├── urls.py                        # Project URL configuration
│   ├── wsgi.py                        # WSGI configuration
│   └── __init__.py
├── onlinecourse/                      # Main application
│   ├── migrations/                    # Database migrations
│   ├── templates/onlinecourse/        # HTML templates
│   │   ├── course_detail_bootstrap.html    # Course detail with exam form
│   │   ├── exam_result_bootstrap.html      # Exam results page
│   │   ├── course_list_bootstrap.html      # Course listing
│   │   ├── user_login_bootstrap.html       # Login page
│   │   └── user_registration_bootstrap.html # Registration page
│   ├── models.py                      # Database models (COMPLETE SOLUTION)
│   ├── views.py                       # View logic (COMPLETE SOLUTION)
│   ├── admin.py                       # Admin configuration (COMPLETE SOLUTION)
│   ├── urls.py                        # App URL configuration
│   ├── apps.py
│   ├── tests.py
│   └── __init__.py
├── static/                            # Static files (CSS, JS, images)
├── manage.py                          # Django management script
├── requirements.txt                   # Python dependencies
├── db.sqlite3                         # SQLite database
├── Procfile                           # For cloud deployment
├── manifest.yml                       # IBM Cloud deployment config
└── README.md                          # Original README

```

## Key Components

### 1. Database Models (models.py)

#### Question Model
- **Purpose**: Represents a quiz question for a course
- **Fields**:
  - `course`: ForeignKey to Course
  - `text`: Question text (CharField, max 200)
  - `grade`: Points for correct answer (FloatField)
- **Method**: `is_get_score(selected)` - Evaluates if submitted answers are correct

#### Choice Model
- **Purpose**: Represents an answer choice for a question
- **Fields**:
  - `question`: ForeignKey to Question
  - `text`: Choice text (CharField, max 200)
  - `is_correct`: Boolean flag for correct answer

#### Submission Model
- **Purpose**: Records a student's exam submission
- **Fields**:
  - `enrollment`: ForeignKey to Enrollment
  - `choices`: ManyToManyField to Choice (selected answers)

### 2. Views (views.py)

#### submit(request, course_id)
- **Purpose**: Handle exam form submission
- **Process**:
  1. Get the course and user
  2. Retrieve the enrollment record
  3. Create a Submission object
  4. Extract selected choices from the form
  5. Add choices to the submission
  6. Redirect to results page

#### show_exam_result(request, course_id, submission_id)
- **Purpose**: Calculate and display exam results
- **Process**:
  1. Retrieve course and submission
  2. Get all submitted choices
  3. Calculate total possible points
  4. Evaluate each question using `is_get_score()`
  5. Calculate final grade (percentage)
  6. Render results template with context

#### extract_answers(request)
- **Purpose**: Parse form data to extract selected choices
- **Returns**: List of Choice objects selected by the student

### 3. Admin Interface (admin.py)

The admin interface is fully configured with:

- **ChoiceInline**: Allows editing choices directly within a question
- **QuestionInline**: Allows editing questions directly within a course
- **CourseAdmin**: Custom admin with lesson and question inlines
- **QuestionAdmin**: Custom admin with choice inlines

All models are registered for easy management:
- Course
- Lesson
- Instructor
- Learner
- Question
- Choice
- Submission

### 4. Templates

#### course_detail_bootstrap.html
- Displays course information and lessons
- Shows "Start Exam" button for authenticated users
- Renders exam form with all questions and choices
- Checkboxes for multiple-choice selection
- Submit button to submit exam

#### exam_result_bootstrap.html
- Displays pass/fail message based on grade threshold (80%)
- Shows final grade as percentage
- Lists all questions with detailed feedback:
  - Green: Correct answers selected
  - Red: Incorrect answers selected
  - Yellow: Correct answers not selected
  - Gray: Incorrect answers not selected
- "Re-test" link for failed exams

## Installation & Setup

### Prerequisites
- Python 3.7+
- pip (Python package manager)
- Git

### Step 1: Clone the Repository

```bash
git clone https://github.com/MahmoudAlmodalal/my-course-repo.git
cd my-course-repo
```

### Step 2: Create Virtual Environment

```bash
python3 -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

### Step 3: Install Dependencies

```bash
pip install -r requirements.txt
```

### Step 4: Apply Migrations

```bash
python manage.py migrate
```

### Step 5: Create Superuser

```bash
python manage.py createsuperuser
# Follow prompts to create admin account
```

### Step 6: Run Development Server

```bash
python manage.py runserver
```

Access the application at `http://localhost:8000`

## Usage

### For Administrators

1. **Access Admin Panel**: Go to `/admin` and login with superuser credentials
2. **Create Course**: Add a new course with name, description, and image
3. **Add Lessons**: Add lessons to the course with content
4. **Create Questions**: Add questions to the course with grade value
5. **Add Choices**: For each question, add multiple choices and mark correct ones

### For Students

1. **Register**: Create a new account on the registration page
2. **Enroll**: Browse courses and click "Enroll" to join
3. **View Course**: Access course content and lessons
4. **Take Exam**: Click "Start Exam" to view all questions
5. **Submit**: Select answers and click "Submit"
6. **View Results**: See your grade and detailed feedback

## Grading Logic

The grading system uses the following logic:

1. **Question Evaluation**: A question is marked correct only if:
   - ALL correct choices are selected
   - NO incorrect choices are selected

2. **Score Calculation**:
   - Each question has a grade value (points)
   - Total points = sum of all question grades
   - Achieved points = sum of grades for correctly answered questions
   - Final grade = (Achieved / Total) × 100

3. **Pass/Fail Threshold**: 80%
   - Score ≥ 80%: Pass (green message)
   - Score < 80%: Fail (red message with re-test option)

## API Endpoints

| Endpoint | Method | Purpose |
| --- | --- | --- |
| `/` | GET | Course list view |
| `/course/<id>/` | GET | Course detail view |
| `/course/<id>/enroll/` | GET | Enroll in course |
| `/course/<id>/submit/` | POST | Submit exam |
| `/course/<id>/show_exam_result/<submission_id>/` | GET | View exam results |
| `/login/` | GET, POST | User login |
| `/register/` | GET, POST | User registration |
| `/logout/` | GET | User logout |
| `/admin/` | GET, POST | Django admin interface |

## Database Schema

### Course
- id (PK)
- name
- image
- description
- pub_date
- total_enrollment

### Lesson
- id (PK)
- title
- order
- course_id (FK)
- content

### Question
- id (PK)
- course_id (FK)
- text
- grade

### Choice
- id (PK)
- question_id (FK)
- text
- is_correct

### Enrollment
- id (PK)
- user_id (FK)
- course_id (FK)
- date_enrolled
- mode
- rating

### Submission
- id (PK)
- enrollment_id (FK)

### Submission_choices (M2M)
- id (PK)
- submission_id (FK)
- choice_id (FK)

## Deployment

### IBM Cloud Foundry

1. Install IBM Cloud CLI
2. Login: `ibmcloud login`
3. Deploy: `ibmcloud cf push`

### Heroku

1. Create Procfile (already included)
2. Install Heroku CLI
3. Deploy: `heroku create` and `git push heroku main`

### AWS/Azure/Google Cloud

Configure appropriate deployment files and follow cloud provider documentation.

## Testing

Create test data via admin panel:

1. Create a course: "Python Basics"
2. Add a lesson: "Introduction to Python"
3. Create 2-3 questions with 4-5 choices each
4. Mark appropriate choices as correct
5. Register a test user
6. Enroll in the course
7. Take the exam and verify grading

## Troubleshooting

### Issue: "No such table" error
**Solution**: Run migrations
```bash
python manage.py makemigrations
python manage.py migrate
```

### Issue: Admin panel shows no models
**Solution**: Ensure models are registered in admin.py

### Issue: Exam form not showing
**Solution**: Verify questions exist for the course via admin panel

### Issue: Incorrect grading
**Solution**: Check that `is_get_score()` method logic in Question model is correct

## Code Quality

- **PEP 8 Compliant**: All Python code follows PEP 8 style guidelines
- **DRY Principle**: Code reuse through template inheritance and model methods
- **Security**: CSRF protection, SQL injection prevention, user authentication
- **Performance**: Optimized database queries with select_related and prefetch_related

## Future Enhancements

- Timer for exams
- Question randomization
- Multiple question types (essay, matching, etc.)
- Detailed analytics and reporting
- Email notifications
- Certificate generation
- Peer review system

## Contributing

To contribute to this project:

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Submit a pull request

## License

This project is licensed under the Apache License 2.0 - see LICENSE file for details.

## Support

For issues or questions:
- Check the original README.md
- Review the SOLUTION_README.md
- Check Django documentation: https://docs.djangoproject.com/
- Visit Django community forums

## Author

**Solution Created**: January 2026
**Based on**: IBM Skills Network Django Course
**Repository**: https://github.com/MahmoudAlmodalal/my-course-repo

---

**Note**: This is a complete, production-ready solution. All models, views, and templates are fully implemented and tested. Students can use this as a reference implementation or starting point for their own projects.
