<!--
This file contains instructions intended to be executed at the start of every session. It is handled by the 'start'
skill. Most frameworks provide a direct way to run the start skill by typing `/start` in the chat. This file is the
repository's entry point.

Implement this file as a router that orchestrates the execution of various instructions defined through other files and
corresponding skills.

Example:

There is a list of tasks in the 'tasks/' directory. Sort them alphabetically and use the 'task' skill to execute them
by three agents in parallel. When an agent finishes a task, it should pick the next one from the sorted list until all
tasks are completed.
-->