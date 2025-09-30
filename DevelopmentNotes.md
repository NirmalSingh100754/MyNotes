# Development Notes

## Overview

This project is a Django REST API for a note-taking application. It supports user registration, authentication, and CRUD operations for notes. Below are detailed explanations of the main components and their functionalities.

---

## models.py

### Note Model

- **author**: ForeignKey to Django's built-in User model. Each note is linked to a user. If the user is deleted, their notes are also deleted (`on_delete=models.CASCADE`).
- **title**: CharField for the note's title (max 100 characters).
- **content**: TextField for the note's content.
- **created_at**: DateTimeField that auto-populates with the creation time.
- `__str__` method returns the note's title for easy identification in admin and shell.

---

## serializers.py

### UserSerializer

- Serializes the User model for registration.
- **fields**: `id`, `username`, `password`.
- **extra_kwargs**: Makes `password` write-only for security.
- **create**: Uses `create_user` to ensure the password is hashed.

### NoteSerializer

- Serializes the Note model.
- **fields**: `id`, `author`, `title`, `content`, `created_at`.
- **author**: Read-only, displays the username of the note's author.

---

## views.py

### CreateUserView

- Inherits from `generics.CreateAPIView`.
- Allows anyone to register a new user (`AllowAny` permission).
- Uses `UserSerializer` for input validation and user creation.

### NoteListCreate

- Inherits from `generics.ListCreateAPIView`.
- Requires authentication (`IsAuthenticated`).
- **get_queryset**: Returns only notes belonging to the current user.
- **perform_create**: Saves a new note with the current user as the author.

### NoteDelete

- Inherits from `generics.RetrieveDestroyAPIView`.
- Requires authentication (`IsAuthenticated`).
- **get_queryset**: Ensures users can only delete their own notes.

---

## urls.py

- Maps API endpoints to view classes.
- `notes/`: Lists all notes for the authenticated user and allows note creation (POST).
- `notes/delete/<int:pk>/`: Allows the authenticated user to delete a note by its primary key (id).

---

## Other Files

### admin.py, tests.py, apps.py, **init**.py

- `admin.py`: (Empty) Register models here for Django admin if needed.
- `tests.py`: (Empty) Add unit tests for your API here.
- `apps.py`: Standard Django app config.
- `__init__.py`: Marks the directory as a Python package.

---

## Usage Flow

1. **User Registration**: Send a POST request to the registration endpoint (not shown in urls.py above, but implemented as `CreateUserView`).
2. **Authentication**: Obtain a token (if using JWT or session auth, not shown here).
3. **Note Operations**: Use the provided endpoints to list, create, and delete notes. All note operations are user-specific and require authentication.

---

## Revision Tips

- Review the serializers to understand how data is validated and transformed.
- Check the views for permission logic and how querysets are filtered by user.
- Look at the model to see what data is stored for each note.
- URLs show how endpoints are structured and which views handle them.

Update this file as you add more features or endpoints!

## serializers.py

### UserSerializer

- Fixed a typo in the Meta class: changed `fileds` to `fields`.
- The `fields` attribute now correctly lists the fields to be serialized: `id`, `username`, and `password`.
- Added `extra_kwargs` to ensure the password is write-only for security.
- The `create` method uses `User.objects.create_user(**validated_data)` to properly create a new user with a hashed password.

### NoteSerializer

- (Assumed from current file) Serializes the `Note` model, including fields: `id`, `author`, `title`, `content`, and `created_at`.
- The `author` field is set as read-only and sourced from the related user's username.

## Summary

- The main change was fixing the typo in the `UserSerializer`'s Meta class, which resolves a critical error when registering users via the API.
- The serializers are now correctly configured for secure and functional user and note management in the API.

---

If you make further changes, add a new section here describing what was changed, why, and any important context for future developers.
