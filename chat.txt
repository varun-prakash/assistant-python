import pyttsx3
import speech_recognition as sr
import datetime
import wikipedia
import webbrowser
import os
from urllib.request import urlopen
import ctypes
import smtplib
import pyjokes
import requests
import json
import subprocess
import random
import time
import re
import cv2
import numpy
from tkinter import *
from PIL import Image
window = Tk()

global var
global var1

var = StringVar()
var1 = StringVar()

engine = pyttsx3.init('sapi5')
voices = engine.getProperty('voices')
# print(voices)
engine.setProperty('voice', voices[0].id)


def update(ind):
	frame = frames[(ind) % 100]
	ind += 1
	label.configure(image=frame)
	window.after(100, update, ind)


def sendEmail(to, content):
	server = smtplib.SMTP('smtp.gmail.com', 587)
	server.ehlo()
	server.starttls()
	server.login('your email', 'password')
	server.sendmail('your email', to, content)
	server.close()


def speak(audio):
	engine.say(audio)
	engine.runAndWait()


def wishMe():
	hour = int(datetime.datetime.now().hour)
	if hour >= 0 and hour < 12:
		var.set("Good Morning")
		window.update()
		speak("Good Morning!")
	elif hour >= 12 and hour < 18:
		var.set("Good Afternoon")
		window.update()
		speak("Good Afternoon!")
	else:
		var.set("Good Evening")
		window.update()
		speak("Good Evening!")
	speak("I am Jarvis Sir. Please tell me how may I help you")


def takeCommand():
	r = sr.Recognizer()
	with sr.Microphone() as source:
		var.set("Listening...")
		window.update()
		print("Listening...")
		r.pause_threshold = 1
		audio = r.listen(source)
	try:
		var.set("Reccognizing")
		window.update()
		print("Recognizing...")
		query = r.recognize_google(audio, language='en-in')
		print(f"User said: {query}\n")
	except Exception as e:
		# print(e)
		print("say that again please...")
		return "None"
		var1.set(query)
		window.update()
	return query


def play():
	btn2['state'] = 'disabled'
	btn0['state'] = 'disabled'
	btn1.configure(bg='orange')
	wishMe()

	while True:
		btn1.configure(bg='orange')
		query = takeCommand().lower()
		if 'exit' in query:
			var.set("Bye sir")
			btn1.configure(bg = '#5C85FB')
			btn2['state'] = 'normal'
			btn0['state'] = 'normal'
			window.update()
			speak("thank you sir")
			break

		elif 'wikipedia' in query:
			speak('Searching Wikipedia......')
			query = query.replace("wikipedia","")
			results = wikipedia.summary(query, sentences=2)
			speak('According to wikipedia')
			print(results)
			var.set(results)
			window.update()
			speak(results)
	
		elif 'open youtube' in query:
			webbrowser.get('C:/Program Files (x86)/Google/Chrome/Application/chrome.exe %s').open('https://www.youtube.com/')

		elif 'open google' in query:
			webbrowser.get('C:/Program Files (x86)/Google/Chrome/Application/chrome.exe %s').open('https://www.google.com/')

		elif 'play music' in query:
			music_dir = 'D:\\music'
			songs = os.listdir(music_dir)
			print(songs)
			play = random.choice(songs)
			print('playing..........'+play)
			os.startfile(os.path.join(music_dir, play))

		elif 'time' in query:
			strTime = datetime.datetime.now().strftime('%I:%M:%S')
			speak('Sir, The time is'+strTime)
			print(strTime)

		elif 'notepad' in query:
			codepath = 'C:\\Users\\Dell\\AppData\\Roaming\\Microsoft\\Windows\\Start Menu\\Programs\\Accessories\\notepad'
			os.startfile(codepath)

		elif 'open mail' in query:
			webbrowser.get('C:/Program Files (x86)/Google/Chrome/Application/chrome.exe %s').open('gmail.com')

		elif 'send email' in query:
			try:
				var.set("what should i send")
				window.update()
				speak('what should I send')
				content = takeCommand()
				mydict = {'chirag':'xyz360@gmail.com',
				'ram':'lastknight03111998@gmail.com',
				'varun':'abc@gmail.com',
				'cat':'ritikmiglani488@gmail.com',
				'rajan':'rdfdfc.07@gmail.com ',
				'suman':'sudfdzdvy039@gmail.com ',
				'jaaaa': 'jfdlslsa25@gmail.com '} 
				print(mydict)
				speak('tell the name of person to send mail')
				us = takeCommand()
				us = us.lower()
				to = mydict[us]
				print(to)
				sendEmail(to,content)
				var.set("email has been sent")
				window.update()
				speak('Email has been sent..')
			except Exception as e:
				# print(e)
				var.set("sorry not able to send the mail")
				window.update()
				speak('sorry not able to send the mail')

		elif 'crack joke' in query:
			l = pyjokes.get_joke()
			print(l)
			speak(l)
		
		elif 'search' in query:
			query = query.replace('search',"")
			webbrowser.open(query)

		elif 'exit' in query:
			speak('I hope you liked my work!!!')
			speak('Have a nice day!!')
			exit()

		elif 'lock windows' in query:
			speak('sir, are you sure')
			x=takeCommand().lower()
			if x=='yes':
				speak('locking the device')
				ctypes.windll.user32.LockWorkStation()
			elif x=='no':
				exit()	
			else:
				speak('sorry sir,i did not get it')

		elif 'shutdown system' in query:
			speak('sir, are you sure')
			y=takeCommand().lower()
			if y=='yes':
				speak('Hold a sec!! starting process of shuting down system')
				subprocess.call('shutdown /p /f')
			elif y=='no':
				exit()	
			else:
				speak('sorry sir,i did not get it')	

		elif 'where is' in query:
			query = query.replace('where is','')
			location = query
			speak('locating on google maps'+location)
			webbrowser.open('https://www.google.com/maps/place/'+location)

		elif 'weather' in query:
			api_key = "229a8ef857058a20b83532792ad0e0a5"
			base_url = "http://api.openweathermap.org/data/2.5/weather?"
			speak("Please tell City name to check weather")
			city_name = takeCommand()
			complete_url = base_url + "appid=" + api_key + "&q=" + city_name 
			response = requests.get(complete_url)
			x = response.json()
			if x["cod"] != "404":
				y = x["main"]
				current_temperature = y["temp"]
				current_pressure = y["pressure"]
				current_humidiy = y["humidity"]
				z = x["weather"]
				weather_description = z[0]["description"]
				print(" Temperature (in kelvin unit) = " +str(current_temperature)+"\n atmospheric pressure (in hPa unit) ="+str(current_pressure) +"\n humidity (in percentage) = " +str(current_humidiy) +"\n description = " +str(weather_description))
			else:
				speak(" City Not Found ")

		elif 'news' in query:
			try:
				my_api_key='270d2ec6e4884e21b67c68e623a8fab3'
				jsonObj = urlopen('https://newsapi.org/v1/articles?source=bbc-news&sortBy=top&apiKey='+my_api_key)
				data = json.load(jsonObj)
				i = 1
				speak('here are some top news from the BBC news')
				print('=============== BBC ============'+ '\n')
				for item in data['articles']:
					print(str(i) + '. ' + item['title'] + '\n')
					print(item['description'] + '\n')
					speak(str(i) + '. ' + item['title'] + '\n')
					i += 1
			except Exception as e:
				print(str(e))
		
		elif 'set timer' in query:
			t = [float(s) for s in re.findall(r'-?\d+\.?\d*', query)]
			s=query
			if 'minutes' in s:
				 tt=t[0]*60

			elif 'hours' in s:
				tt = t[0]*3600
					
			else:
				tt=t[0]

			for i in range(tt):
				print(str(tt-i))
				b=str(tt-i)
				speak(b)

		elif 'click photo' in query:
			stream = cv2.VideoCapture(0)
			grabbed, frame = stream.read()
			
			if grabbed:
				cv2.imshow('pic', frame)
				cv2.imwrite('pic.jpg',frame)
			stream.release()


label2 = Label(window, textvariable = var1, bg = '#FAB60C')
label2.config(font=("Courier", 20))
var1.set('User Said:')
label2.pack()

label1 = Label(window, textvariable = var, bg = '#ADD8E6')
label1.config(font=("Courier", 20))
var.set('Welcome')
label1.pack()

frames = [PhotoImage(file='Assistant.gif',format = 'gif -index %i' %(i)) for i in range(100)]
window.title('JARVIS')

label = Label(window, width = 500, height = 500)
label.pack()
window.after(0, update, 0)

btn0 = Button(text = 'WISH ME',width = 20, command = wishMe, bg = '#5C85FB')
btn0.config(font=("Courier", 12))
btn0.pack()
btn1 = Button(text = 'PLAY',width = 20,command = play, bg = '#5C85FB')
btn1.config(font=("Courier", 12))
btn1.pack()
btn2 = Button(text = 'EXIT',width = 20, command = window.destroy, bg = '#5C85FB')
btn2.config(font=("Courier", 12))
btn2.pack()


window.mainloop()
			
			 
			
		   
			   
				
			

