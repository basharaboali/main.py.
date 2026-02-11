from kivy.app import App
from kivy.uix.boxlayout import BoxLayout
from kivy.uix.label import Label
from kivy.uix.textinput import TextInput
from kivy.uix.button import Button
import threading
import asyncio
import websockets

class GeminiChat(App):
    def build(self):
        self.layout = BoxLayout(orientation='vertical', padding=10, spacing=5)
        self.history = Label(text="جاري الاتصال بالسحاب...\n", size_hint_y=0.8, halign="right", valign="top")
        self.history.bind(size=self.history.setter('text_size'))
        self.layout.add_widget(self.history)
        
        self.input = TextInput(hint_text="اكتب رسالتك هنا...", multiline=False, size_hint_y=0.1)
        self.layout.add_widget(self.input)
        
        self.btn = Button(text="إرسال", size_hint_y=0.1, background_color=(0, 0.6, 0.4, 1))
        self.btn.bind(on_press=self.send_wrapper)
        self.layout.add_widget(self.btn)
        
        self.ws = None
        self.loop = None
        threading.Thread(target=self.start_async_loop, daemon=True).start()
        return self.layout

    def start_async_loop(self):
        self.loop = asyncio.new_event_loop()
        asyncio.set_event_loop(self.loop)
        self.loop.run_until_complete(self.connect())

    async def connect(self):
        # الرابط الخاص بك من Serveo
        uri = "wss://29865d7a8651ed5a-156-146-55-209.serveousercontent.com"
        try:
            async with websockets.connect(uri) as websocket:
                self.ws = websocket
                self.history.text += "[نظام]: تم الاتصال بنجاح!\n"
                while True:
                    message = await websocket.recv()
                    self.history.text += f"طرف آخر: {message}\n"
        except Exception as e:
            self.history.text += f"[خطأ]: {str(e)}\n"

    def send_wrapper(self, instance):
        if self.input.text and self.ws:
            msg = self.input.text
            self.history.text += f"أنت: {msg}\n"
            # إرسال الرسالة فعلياً عبر السيرفر
            asyncio.run_coroutine_threadsafe(self.ws.send(msg), self.loop)
            self.input.text = ""

if __name__ == "__main__":
    GeminiChat().run()
