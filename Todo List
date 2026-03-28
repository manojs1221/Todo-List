import os
import re # We need this to find and fix the leading zeros

# Graphics and Input Fixes for Windows Laptops
os.environ['KIVY_GL_BACKEND'] = 'angle_sdl2'
os.environ['KIVY_NO_ARGS'] = '1'

from kivy.config import Config
Config.set('input', 'mouse', 'mouse,disable_multitouch')

import json
from kivy.app import App
from kivy.uix.boxlayout import BoxLayout
from kivy.lang import Builder
from kivy.properties import StringProperty, BooleanProperty

KV = '''
<TaskRow>:
    orientation: 'horizontal'
    size_hint_y: None
    height: '60dp'
    padding: [10, 5]
    spacing: 10
    canvas.before:
        Color:
            rgba: (0.15, 0.15, 0.15, 1)
        RoundedRectangle:
            pos: self.pos
            size: self.size
            radius: [10,]

    CheckBox:
        active: root.is_done
        size_hint_x: None
        width: '40dp'
        on_active: app.toggle_task(root.text, self.active)

    Label:
        text: root.text
        halign: 'left'
        text_size: self.size
        valign: 'middle'
        # This adds the strikethrough effect using Kivy markup
        markup: True
        color: (0.5, 0.5, 0.5, 1) if root.is_done else (1, 1, 1, 1)
        text: f"[s]{root.text}[/s]" if root.is_done else root.text

    Button:
        text: 'DEL'
        size_hint_x: None
        width: '60dp'
        background_color: (0.8, 0.3, 0.3, 1)
        on_release: app.remove_task(root.text)

BoxLayout:
    orientation: 'vertical'
    padding: 15
    spacing: 15
    canvas.before:
        Color:
            rgba: (0.05, 0.05, 0.05, 1)
        Rectangle:
            pos: self.pos
            size: self.size

    Label:
        text: "Internship Task Tracker"
        font_size: '28sp'
        bold: True
        size_hint_y: None
        height: '50dp'

    BoxLayout:
        size_hint_y: None
        height: '50dp'
        spacing: 10
        TextInput:
            id: task_input
            multiline: False
            hint_text: "What needs to be done?"
            padding_y: [15, 0]
        Button:
            text: "ADD"
            bold: True
            size_hint_x: None
            width: '80dp'
            background_color: (0.2, 0.6, 0.2, 1)
            on_release: app.add_task(task_input.text); task_input.text = ""

    RecycleView:
        id: task_list
        viewclass: 'TaskRow'
        RecycleBoxLayout:
            default_size: None, dp(65)
            default_size_hint: 1, None
            size_hint_y: None
            height: self.minimum_height
            orientation: 'vertical'
            spacing: 8
'''

class TaskRow(BoxLayout):
    text = StringProperty('')
    is_done = BooleanProperty(False)

class ToDoApp(App):
    def build(self):
        self.data_file = "tasks.json"
        self.tasks = self.load_tasks()
        return Builder.load_string(KV)

    def load_tasks(self):
        try:
            with open(self.data_file, 'r') as f:
                # Store tasks as a list of dictionaries: [{"text": "...", "done": False}]
                return json.load(f)
        except:
            return []

    def on_start(self):
        self.update_ui()

    def add_task(self, task_text):
        if task_text.strip():
            self.tasks.append({"text": task_text, "done": False})
            self.update_ui()
            self.save_tasks()

    def toggle_task(self, task_text, is_active):
        for task in self.tasks:
            if task['text'] == task_text:
                task['done'] = is_active
                break
        self.update_ui()
        self.save_tasks()

    def remove_task(self, task_text):
        self.tasks = [t for t in self.tasks if t['text'] != task_text]
        self.update_ui()
        self.save_tasks()

    def update_ui(self):
        # Maps our dictionary to the RecycleView's format
        self.root.ids.task_list.data = [{'text': t['text'], 'is_done': t['done']} for t in self.tasks]

    def save_tasks(self):
        with open(self.data_file, 'w') as f:
            json.dump(self.tasks, f)

if __name__ == '__main__':
    ToDoApp().run()
