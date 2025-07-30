import sys
import json
import os
from datetime import datetime

TASK_FILE = 'tasks.json'

def load_tasks():
    if not os.path.exists(TASK_FILE):
        return []
    with open(TASK_FILE, 'r') as file:
        return json.load(file)

def save_tasks(tasks):
    with open(TASK_FILE, 'w') as file:
        json.dump(tasks, file, indent=4)

def get_next_id(tasks):
    return max((task['id'] for task in tasks), default=0) + 1

def current_time():
    return datetime.now().isoformat()

def add_task(description):
    tasks = load_tasks()
    task = {
        "id": get_next_id(tasks),
        "description": description,
        "status": "todo",
        "createdAt": current_time(),
        "updatedAt": current_time()
    }
    tasks.append(task)
    save_tasks(tasks)
    print(f"Task added successfully (ID: {task['id']})")

def update_task(task_id, new_description):
    tasks = load_tasks()
    for task in tasks:
        if task['id'] == task_id:
            task['description'] = new_description
            task['updatedAt'] = current_time()
            save_tasks(tasks)
            print("Task updated successfully")
            return
    print("Task not found")

def delete_task(task_id):
    tasks = load_tasks()
    new_tasks = [task for task in tasks if task['id'] != task_id]
    if len(new_tasks) != len(tasks):
        save_tasks(new_tasks)
        print("Task deleted successfully")
    else:
        print("Task not found")

def mark_status(task_id, status):
    tasks = load_tasks()
    for task in tasks:
        if task['id'] == task_id:
            task['status'] = status
            task['updatedAt'] = current_time()
            save_tasks(tasks)
            print(f"Task marked as {status}")
            return
    print("Task not found")

def list_tasks(status_filter=None):
    tasks = load_tasks()
    filtered_tasks = tasks if status_filter is None else [t for t in tasks if t['status'] == status_filter]

    if not filtered_tasks:
        print("No tasks found.")
        return

    for task in filtered_tasks:
        print(f"ID: {task['id']} | {task['status'].upper()} | {task['description']} | Created: {task['createdAt']} | Updated: {task['updatedAt']}")

def main():
    if len(sys.argv) < 2:
        print("No command provided.")
        return

    command = sys.argv[1]

    if command == "add" and len(sys.argv) >= 3:
        description = " ".join(sys.argv[2:])
        add_task(description)
    elif command == "update" and len(sys.argv) >= 4:
        update_task(int(sys.argv[2]), " ".join(sys.argv[3:]))
    elif command == "delete" and len(sys.argv) == 3:
        delete_task(int(sys.argv[2]))
    elif command == "mark-in-progress" and len(sys.argv) == 3:
        mark_status(int(sys.argv[2]), "in-progress")
    elif command == "mark-done" and len(sys.argv) == 3:
        mark_status(int(sys.argv[2]), "done")
    elif command == "list":
        if len(sys.argv) == 3:
            list_tasks(sys.argv[2])
        else:
            list_tasks()
    else:
        print("Invalid command or arguments.")

if __name__ == "__main__":
    main()
    ## Project Description
    This is a Task Tracker project from roadmap.sh.
    ##Project URL
    https://roadmap.sh/projects/task-tracker
