# openai-plan-quickstart

- 以下是一个代码上的例子。
 
```
import json
from typing import List, Tuple

# Define simplified versions of your action and observation classes
class Action:
    def __init__(self, action_type, args=None):
        self.action_type = action_type
        self.args = args if args else {}

    def to_memory(self):
        return {'action_type': self.action_type, 'args': self.args}

    def to_dict(self):
        return {'action': self.action_type, 'args': self.args}

class Observation:
    def __init__(self, data):
        self.data = data

    def to_dict(self):
        return {'data': self.data}

class Plan:
    def __init__(self, main_goal):
        self.main_goal = main_goal
        self.task = {"goal": main_goal, "subtasks": []}

    def to_dict(self):
        return self.task

# Assuming ActionType as constants
class ActionType:
    RUN = 'run'
    WRITE = 'write'

# Define main function
def get_prompt(plan, history):
    plan_str = json.dumps(plan.to_dict(), indent=2)
    history_str = json.dumps([item.to_memory() for item in history], indent=2)
    prompt = f"""
# Task
You've been given the following task:
{plan.main_goal}

## Plan
{plan_str}

## History
{history_str}

What is your next thought or action? Your response must be in JSON format.
"""
    return prompt

def parse_response(response):
    action_dict = json.loads(response)
    return Action(action_dict['action'], action_dict.get('args'))

def main():
    # Create an initial task and plan
    initial_plan = Plan("Optimize the performance of the web server.")

    # Example history of actions taken
    history = [
        Action(ActionType.RUN, {'command': 'ls'}),
        Action(ActionType.RUN, {'command': 'uptime'})
    ]

    # Generate the initial prompt using the current plan and history
    prompt_text = get_prompt(initial_plan, history)
    print("Generated Prompt:\n", prompt_text)

    # Simulate a response from the AI model (for testing purposes)
    simulated_response = '{"action": "write", "args": {"path": "/etc/nginx/nginx.conf", "content": "server optimization settings"}}'
    
    # Parse the response to get the next action
    next_action = parse_response(simulated_response)
    print("Next Action to Perform:", next_action.to_dict())

if __name__ == "__main__":
    main()
```
