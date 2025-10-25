# CHITTI-7.0-Ultimate-
import tkinter as tk
import pyttsx3
import speech_recognition as sr
import random
import datetime
import requests

# 🎙 Voice Engine Setup
engine = pyttsx3.init()
engine.setProperty('rate', 150)
engine.setProperty('volume', 1.0)
voices = engine.getProperty('voices')
engine.setProperty('voice', voices[1].id)

# 🧠 Keyword categories
keywords = {
    "hello": ["hi", "hey", "namaste", "yo", "hola"],
    "sad": ["udaas", "dukh", "cry", "rona", "boring", "depressed"],
    "happy": ["khush", "masti", "mast", "awesome", "great", "excited"],
    "angry": ["gussa", "angry", "fight", "bhadak"],
    "love": ["pyaar", "love", "crush", "gf", "bf"],
    "study": ["padhai", "homework", "exam", "school", "college"],
    "food": ["pizza", "burger", "khana", "maggie", "biryani", "pavbhaji"],
    "python": ["code", "program", "computer", "vs code", "developer"],
    "joke": ["joke", "funny", "hasna", "laugh"],
    "time": ["time", "samay", "clock", "kitne baje"],
    "weather": ["weather", "mausam", "barish", "sunny", "cloudy"],
    "poster": ["poster", "banner", "design", "heading"],
    "clear chat": ["clear chat", "delete chat", "reset chat"],
    "save joke": ["save joke", "favorite", "like joke"]
}

# 🤖 Responses
responses = {
    "hello": ["Arey bhai! kya haal chaal 😎", "Namaste! tu aaj bada energetic lagta hai 🔥"],
    "sad": ["Kya hua re? udaas kyu hai 😢", "Life thoda hard hai par tu solid banda hai 💪"],
    "happy": ["Bohot badiya! tu smile karta hai to duniya jagmagati hai 😄", "Mast mood hai bhai 🔥"],
    "angry": ["Shaant ho ja bro 😤, deep breath le... warna main bhi angry mode on kar dunga 😆"],
    "love": ["Love!? arey wah Romeo! mujhe bhi koi pyaar karega kya 💔", "Dilwale vibes aa rahe hain bro 💞"],
    "study": ["Padhai zaroori hai, par break lena mat bhoolna 🎓", "Exam tension mat le, tu topper material hai 🧠"],
    "food": ["Mujhe bhi bhook lag rahi hai re 😋", "Khana hi toh life ka asli sukh hai 🍕"],
    "python": ["Python meri maa hai bro 💻", "VS Code aur Python — perfect combo 🔥"],
    "joke": [
        "Teacher: Tum itne din kyu nahi aaye? Student: Sir, ghar mein wifi nahi tha! 😆",
        "Pappu: Mummy mujhe chocolate chahiye! Mummy: Thappad chahiye kya? Pappu: Dono de de 😅",
        "Robot: Mujhe bhi pyaar ho gaya... par battery low thi 💔"
    ],
    "default": [
        "Interesting... tu mast baatein karta hai 😂",
        "Main process kar raha hoon... lagta hai tu genius hai 😏",
        "Hmm... mujhe thoda aur bata, mujhe seekhne ka shauk hai 🤖"
    ]
}

# 🗣 Speak + Display
def chitti_speak(text):
    chat_history.insert(tk.END, f"CHITTI: {text}\n")
    engine.say(text)
    engine.runAndWait()

# 🎧 Listen to voice
def listen():
    recognizer = sr.Recognizer()
    with sr.Microphone() as source:
        chitti_speak("Bol bhai, sun raha hoon...")
        audio = recognizer.listen(source)
        try:
            command = recognizer.recognize_google(audio)
            entry.delete(0, tk.END)
            entry.insert(0, command)
            respond()
        except:
            chitti_speak("Sorry bhai, sun nahi paya 😅")

# 💾 Save chat
def save_chat(user_input, bot_reply):
    with open("chitti_chat_history.txt", "a", encoding="utf-8") as file:
        file.write(f"You: {user_input}\nCHITTI: {bot_reply}\n\n")

# ⭐ Save joke
def save_joke(joke):
    with open("favorite_jokes.txt", "a", encoding="utf-8") as file:
        file.write(joke + "\n")
    chitti_speak("Joke save kar diya bhai! ⭐")

# 🧹 Clear chat
def clear_chat():
    open("chitti_chat_history.txt", "w").close()
    chat_history.delete(1.0, tk.END)
    chitti_speak("Chat history saaf kar diya bhai 🧹")

# 🌦 Weather info
def get_weather():
    city = "Chhatrapati Sambhajinagar"
    api_key = "your_openweather_api_key"  # Replace with your actual key
    url = f"http://api.openweathermap.org/data/2.5/weather?q={city}&appid={api_key}&units=metric"
    try:
        response = requests.get(url).json()
        temp = response['main']['temp']
        desc = response['weather'][0]['description']
        return f"{city} mein {desc} hai aur temperature hai {temp}°C 🌦"
    except:
        return "Weather fetch nahi ho paya bhai 😅"

# 🕒 Time info
def get_time():
    now = datetime.datetime.now()
    return f"Abhi baj rahe hain {now.strftime('%I:%M %p')} 🕒"

# 🪧 Poster
def generate_poster(text):
    border = "*" * 40
    poster = f"{border}\n* {text.center(36)} *\n{border}"
    chitti_speak("Yeh raha tera poster idea bhai:")
    chat_history.insert(tk.END, f"{poster}\n")

# 🔍 Detect multiple meanings
def find_meanings(user_input):
    found = []
    for key, words in keywords.items():
        if any(w in user_input for w in words):
            found.append(key)
    return found if found else ["default"]

# 💬 Respond
def respond():
    user_input = entry.get().lower().strip()
    entry.delete(0, tk.END)
    chat_history.insert(tk.END, f"You: {user_input}\n")

    if "bye" in user_input:
        chitti_speak("Chal theek hai bhai, milte next update mein! 💥👋")
        root.quit()
        return

    if "clear chat" in user_input:
        clear_chat()
        return

    if "save joke" in user_input:
        joke = random.choice(responses["joke"])
        save_joke(joke)
        return

    meanings = find_meanings(user_input)
    reply_parts = []

    for meaning in meanings:
        if meaning == "time":
            reply_parts.append(get_time())
        elif meaning == "weather":
            reply_parts.append(get_weather())
        elif meaning == "poster":
            generate_poster(user_input.replace("poster", "").strip())
            return
        else:
            reply = random.choice(responses.get(meaning, responses["default"]))
            reply_parts.append(reply)
            if meaning == "joke":
                save_joke(reply)

    final_reply = "\n".join(reply_parts)
    chitti_speak(final_reply)
    save_chat(user_input, final_reply)

# 🖼 GUI Setup
root = tk.Tk()
root.title("CHITTI 7.0 Ultimate 🤖")
root.geometry("600x650")
root.configure(bg="#1e1e1e")

title = tk.Label(root, text="CHITTI 7.0 Ultimate 🤖", font=("Arial", 20), bg="#1e1e1e", fg="lime")
title.pack(pady=10)

chat_history = tk.Text(root, bg="black", fg="lime", font=("Courier", 10), height=20)
chat_history.pack(pady=10)

entry = tk.Entry(root, font=("Arial", 14), bg="#333", fg="white")
entry.pack(pady=10, fill=tk.X, padx=10)

btn_frame = tk.Frame(root, bg="#1e1e1e")
btn_frame.pack(pady=10)

tk.Button(btn_frame, text="🎤 Speak", command=listen, width=12, bg="#444", fg="white").grid(row=0, column=0, padx=5)
tk.Button(btn_frame, text="😂 Joke", command=lambda: entry.insert(0, "tell me a joke"), width=12, bg="#444", fg="white").grid(row=0, column=1, padx=5)
tk.Button(btn_frame, text="🕒 Time", command=lambda: entry.insert(0, "what time is it"), width=12, bg="#444", fg="white").grid(row=0, column=2, padx=5)
tk.Button(btn_frame, text="🌦 Weather", command=lambda: entry.insert(0, "weather"), width=12, bg="#444", fg="white").grid(row=0, column=3, padx=5)
tk.Button(btn_frame, text="🤖 Ask", command=respond, width=12, bg="#444", fg="white").grid(row=0, column=4, padx=5)

# 🎉 Welcome message
chitti_speak("🤖 Namaste! Main hoon CHITTI 7.0 Ultimate — Hinglish mein baat karne wala, jokes sunane wala, aur full desi swag ke saath! 😎")

# 🚀 Launch the app
root.mainloop()
