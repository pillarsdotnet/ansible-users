To-Do
=====

- When purging a user, consider searching through the filesystem for user-owned files outside the home directory. Such files should be deleted if found within a temp directory, and chowned to another user otherwise. If chowned, the setuid and setgid bits should be cleared.
- Integrate with mailsystem to forward local mail by managing mail aliases.
