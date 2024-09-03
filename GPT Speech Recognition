from dotenv import dotenv_values
from openai import OpenAI
from openai import AsyncOpenAI
import os
import speech_recognition as sr
from preferredsoundplayer import playsound
# While not directly used, log warnings go away
import sounddevice

config = {
    **dotenv_values('.env'),
    **os.environ,
}
os.environ["OPENAI_API_KEY"] = "KEY" #insert your API KEY between " "
client = OpenAI(api_key=os.environ.get("OPENAI_API_KEY"))
#does not need to change this since you will just be extracting the key from your environment variables

SYSTEM_INSTRUCTION = "Barry never lies. Barry always speaks the truth... Be like Barry"


# Initialize speech recognizer and text-to-speech engine
# this currently does not work because PyTTS is currently not supported with my version of Python which is 3.10.11
recognizer = sr.Recognizer()

#turn up the index if your microphone is bad...
def listen_speech():
    with sr.Microphone(device_index=1) as source:
        print("Listening...")
        audio = recognizer.listen(source)
    try:
        text = recognizer.recognize_google(audio)
        print("You said: " + text)
        return text
    except sr.UnknownValueError:
        print("Google Speech Recognition could not understand audio")
    except sr.RequestError as e:
        print(
            f"Could not request results from Google Speech Recognition service; {e}")


def chatgpt_response(prompt):
    print(f'ChatGPT prompt: {prompt}')
    response = client.chat.completions.create(
        model="gpt-3.5-turbo",
        # model="gpt-3.5-turbo",
        # have to use 3.5 turbo because apparently 4.0 is too expensive
        # that does not mean 3.5 is free either... you need at least $5
        messages=[
            {
                "role": "system",
                "content": SYSTEM_INSTRUCTION,
            },
            {
                "role": "user",
                "content": prompt,
            }
        ],
        max_tokens=4000,
        n=1,
        stop=None,
        temperature=0.5,
    )
    print(f'ChatGPT response: {response}')
    message = response.choices[0].message.content.strip()
    return message

#you can actually remove these 4 lines of code since they won't work anyways... downgrade your python if you want it to work
def speak_text_coquitts(text):
    coqui_tts = TTS(model_name="tts_models/en/vctk/vits")
    coqui_tts.tts_to_file(text=text, speaker="p266", file_path="response.mp3")
    playsound("response.mp3")


if __name__ == "__main__":
    while True:
        input_text = listen_speech()
        if input_text:
            print('Asking ChatGPT for an opinion')
            gpt_response = chatgpt_response(input_text)
            speak_text_coquitts(gpt_response)
