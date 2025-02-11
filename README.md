## Simple command line interface (CLI)

import sys
import json
from datetime import datetime

# JSON file to store tasks
task_file = "tasks.json"

# Load tasks from the JSON file or create an empty file if it doesn't exist
def load_tasks():
    try:
        with open(task_file, "r") as file:
            return json.load(file)
    except FileNotFoundError:
        return []

def save_tasks(tasks):
    with open(task_file, "w") as file:
        json.dump(tasks, file, indent=4)

# Generate a unique ID for tasks
def generate_task_id(tasks):
    return max(task["id"] for task in tasks) + 1 if tasks else 1

# Add a new task
def add_task(description):
    tasks = load_tasks()
    new_task = {
        "id": generate_task_id(tasks),
        "description": description,
        "status": "todo",
        "createdAt": datetime.now().isoformat(),
        "updatedAt": datetime.now().isoformat()
    }
    tasks.append(new_task)
    save_tasks(tasks)
    print(f"Task added successfully (ID: {new_task['id']})")

# Update a task
def update_task(task_id, new_description):
    tasks = load_tasks()
    for task in tasks:
        if task["id"] == task_id:
            task["description"] = new_description
            task["updatedAt"] = datetime.now().isoformat()
            save_tasks(tasks)
            print(f"Task ID {task_id} updated successfully.")
            return
    print(f"Task ID {task_id} not found.")

# Delete a task
def delete_task(task_id):
    tasks = load_tasks()
    tasks = [task for task in tasks if task["id"] != task_id]
    save_tasks(tasks)
    print(f"Task ID {task_id} deleted successfully.")

# Mark a task as in-progress or done
def mark_task(task_id, status):
    tasks = load_tasks()
    for task in tasks:
        if task["id"] == task_id:
            task["status"] = status
            task["updatedAt"] = datetime.now().isoformat()
            save_tasks(tasks)
            print(f"Task ID {task_id} marked as {status}.")
            return
    print(f"Task ID {task_id} not found.")

# List tasks based on status or all tasks
def list_tasks(status=None):
    tasks = load_tasks()
    if status:
        tasks = [task for task in tasks if task["status"] == status]
    if tasks:
        for task in tasks:
            print(f"ID: {task['id']}, Description: {task['description']}, Status: {task['status']}, Created At: {task['createdAt']}, Updated At: {task['updatedAt']}")
    else:
        print("No tasks found.")

# Main function to handle CLI commands
def main():
    if len(sys.argv) < 2:
        print("Usage: task-cli <command> [arguments]")
        return

    command = sys.argv[1]

    if command == "add" and len(sys.argv) >= 3:
        add_task(" ".join(sys.argv[2:]))
    elif command == "update" and len(sys.argv) >= 4:
        try:
            update_task(int(sys.argv[2]), " ".join(sys.argv[3:]))
        except ValueError:
            print("Invalid task ID.")
    elif command == "delete" and len(sys.argv) >= 3:
        try:
            delete_task(int(sys.argv[2]))
        except ValueError:
            print("Invalid task ID.")
    elif command == "mark-in-progress" and len(sys.argv) >= 3:
        try:
            mark_task(int(sys.argv[2]), "in-progress")
        except ValueError:
            print("Invalid task ID.")
    elif command == "mark-done" and len(sys.argv) >= 3:
        try:
            mark_task(int(sys.argv[2]), "done")
        except ValueError:
            print("Invalid task ID.")
    elif command == "list":
        if len(sys.argv) == 3:
            list_tasks(sys.argv[2])
        else:
            list_tasks()
    else:
        print("Invalid command or arguments.")

if __name__ == "__main__":
    main()
