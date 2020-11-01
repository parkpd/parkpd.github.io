---
title: Cubase 드럼맵 파일을 drm <-> text 형태로 변환하는 파이썬 코드
date: 2020-11-01 23:30:00+0900
categories: Python
tags: [Cubase, Yamaha, Roland, Midi, Python, 파이썬]
---
올해 7월에 이미 비슷한 코드를 만들었는데 이걸 왜 또 만들었냐면,  
* 정말 7월에 저런 코드를 만들었다는 걸 까먹었기 때문이고...(뭔가 기시감은 있었는데)
* 좀 더 향상된 기능이 필요했기 때문이다.

이전에는 drm 파일을 text 파일로 변경만 가능했다면, 이제는 이렇게 변경된 text 포멧으로부터 drm 파일을 생성할 수 있다.

NI 에서 만든 [STUDIO DRUMMER](https://www.native-instruments.com/en/products/komplete/drums/studio-drummer/) 를 쓰려고 보니 drm 파일이 부정확한 게 이 코드를 만들게 된 첫 번째 이유다.

{% highlight python %}
|| C1 || KICK Dampened ||
|| C#1 || SNARE Side Stick ||
|| D1 || SNARE Centre Alt ||
{% endhighlight %}

으로 표현된 텍스트 파일에서 원하는 미디 노트와 드럼 키 이름을 입력한 뒤에 이 파일을 실행인자로 넘기면 txt 파일에서 drm 파일을 생성할 수 있다.
이 작업을 하면서 [middle C 가 프로그램마다 왜 다르게 되었는지](https://music.stackexchange.com/questions/70519/what-is-rolands-pitch-and-yamahas-pitch-and-how-to-convert-notes-to-them)도 대강 알게 되었다.
(Cubase 는 C4 가 72, AKAI 는 C4 가 60)

Visual Studio 2019 에 Python 용 unittest 프레임워크를 이용하면 Run Tests 를 할 수 있다는 것도,
["Use adaptive formatting" 옵션](https://developercommunity.visualstudio.com/content/problem/847853/visual-studio-20194-c-insert-spaces-instead-of-tab.html)이 켜져 있으면 tab 을 자꾸 space 로 바꿀 가능성이 있다는 것도 알게 되었다.

{% highlight python %}
import os
import pathlib
import sys, getopt
from bs4 import BeautifulSoup

# Middle C on a piano is C4
# midi note 36 이 Cubase 에서는 C1 인데 AKAI 에서는 C2 다.
# Roland(AKAI) 는 middle c(60) 를 C4 로 쓰고, Yamaha(Cubase) 는 C3 로 쓴다.
# 역사적으로는 Roland C4 가 표준인 거 같지만, 내가 쓰는 대부분의 소프트웨어가 C3 를 쓰므로
# 여기에서는 C3 를 기본값으로 한다.
def get_octave_modifier(using_middle_c_c4):
	if using_middle_c_c4 :
		return 1
	else:
		return 2

def convert_num_to_note(inote, using_middle_c_c4):
	note_array = ["C", "C#", "D", "D#", "E", "F", "F#", "G", "G#", "A", "A#", "B"]
	octave, interval = divmod(inote, 12)
	ret = note_array[interval] + str(octave - get_octave_modifier(using_middle_c_c4))
	return ret

def split_note_octave(inote_str):
	split_index = 1
	if inote_str[1] == "#":
		split_index = 2
	note_str = inote_str[0:split_index]
	octave_str = inote_str[split_index:]
	return note_str, octave_str

def convert_note_to_num(inote_str, using_middle_c_c4):
	note_array = ["C", "C#", "D", "D#", "E", "F", "F#", "G", "G#", "A", "A#", "B"]
	note_str, octave_str = split_note_octave(inote_str)
	octave = int(octave_str)
	note_index = note_array.index(note_str)
	return (octave + get_octave_modifier(using_middle_c_c4)) * 12 + note_index

def get_file_name_without_extension(file_path):
	file_name = os.path.basename(file_path)
	return os.path.splitext(file_name)[0]

def save_drm_as_txt_file(file_path_without_ext, item_list, using_middle_c_c4):
	with open(file_path_without_ext + ".txt", "w") as txt_file:
		for item in item_list:
			note_name = item.find("string")["value"]
			inote_value_str = item.find("int", {"name": "INote"})["value"]
			inote_value = int(inote_value_str)
			str = "|| " + convert_num_to_note(inote_value, using_middle_c_c4) + " || " + note_name + " ||"
			if using_middle_c_c4:
				str += " " + inote_value_str + " ||"
			print(str)
			txt_file.writelines(str + "\n")

def convert_drm_to_txt(file_path):
	file_path_without_ext, _ = os.path.splitext(file_path)
	with open(file_path_without_ext + ".drm", "r") as xml_file:
		soup = BeautifulSoup(xml_file, 'lxml')
		item_map = soup.find("list", {"name": "Map"})
		item_list = item_map.find_all("item")

		save_drm_as_txt_file(file_path_without_ext, item_list, False)
		save_drm_as_txt_file(file_path_without_ext + "_roland_middle_c4", item_list, True)

def convert_txt_to_drm(file_path, using_middle_c_c4):
	file_path_without_ext, _ = os.path.splitext(file_path)
	file_name = get_file_name_without_extension(file_path)
	with open(file_path_without_ext + ".txt", "r") as txt_file, open(file_path_without_ext + ".drm", "w") as xml_file :		
		xml_start = """<?xml version="1.0" encoding="utf-8"?>
<DrumMap>
   <string name="Name" value="%s" wide="true"/>
   <list name="Quantize" type="list">
      <item>
         <int name="Grid" value="4"/>
         <int name="Type" value="0"/>
         <float name="Swing" value="0"/>
         <int name="Legato" value="50"/>
      </item>
   </list>
   <list name="Map" type="list">"""
		xml_file.write(xml_start % file_name)

        # txt 파일을 읽어서 xml 으로 변환한다.
        # 1 부터 127까지 비어있으면 큐베이스의 Pitch 값이 밀려서 표시되므로 꽉 채워줘야 한다.
		list_to_name = ["" for x in range(128)]

		# .txt 파일에서 로딩한 데이터를 list_to_name 에 덮어쓴다.
		lines = txt_file.readlines()
		for line in lines:
			splited_data = line.split("||")
			list_to_name[convert_note_to_num(splited_data[1].strip(), using_middle_c_c4)] = splited_data[2].strip()

		xml_item = """\n      <item>
         <int name="INote" value="{0}"/>
         <int name="ONote" value="{0}"/>
         <int name="Channel" value="-1"/>
         <float name="Length" value="200"/>
         <int name="Mute" value="0"/>
         <int name="DisplayNote" value="{0}"/>
         <int name="HeadSymbol" value="0"/>
         <int name="Voice" value="0"/>
         <int name="PortIndex" value="0"/>
         <string name="Name" value="{1}" wide="true"/>
         <int name="QuantizeIndex" value="0"/>
      </item>"""

		# 0~127 까지 xml node 'item' 를 생성한다.
		for i in range(0, 128):
			print(str(i) + " " + list_to_name[i])
			xml_file.write(xml_item.format(i, list_to_name[i]))

		xml_file.write("""\n   </list>
   <list name="Order" type="int">\n""")

		for i in range(0, 128):
			xml_file.write("""      <item value="{0}"/>\n""".format(i))

		xml_file.write("""   </list>
   <list name="OutputDevices" type="list">
      <item>
         <string name="DeviceName" value="Default Device"/>
         <string name="PortName" value="Default Port"/>
      </item>
   </list>
</DrumMap>""")

def main(file_path):
	file_ext = pathlib.Path(file_path.lower()).suffix;

	# 확장자에 따라 .drm <-> .txt 로 변환한다.
	if file_ext == ".drm" :
		convert_drm_to_txt(file_path)
	elif file_ext == ".txt" :
		convert_txt_to_drm(file_path, False)

if __name__ == '__main__':
	main(sys.argv[1])
    # main("NI SD Garage Full 2.txt")
    # main("NI SD Garage Full 2.drm")
	# main("C:\\Music\\CubaseDrumMap\\Studio Drummer\\NI SD Garage Full 2.drm")
	# main("C:\\Music\\CubaseDrumMap\\Studio Drummer\\NI SD Garage Full 2.txt")
{% endhighlight %}

{% highlight python %}
import DrumMap
import unittest

class CustomTests(unittest.TestCase): 
	def test_convert_num_to_note(self):
		self.assertEqual(DrumMap.convert_num_to_note(0, False), "C-2")
		self.assertEqual(DrumMap.convert_num_to_note(1, False), "C#-2")
		self.assertEqual(DrumMap.convert_num_to_note(36, False), "C1")  # Yamaha, Cubase
		self.assertEqual(DrumMap.convert_num_to_note(36, True), "C2")  # Roland, AKAI

	def test_convert_note_to_num1(self):
		note_str, octave_str = DrumMap.split_note_octave("C#4")
		self.assertEqual(note_str, "C#")
		self.assertEqual(octave_str, "4")

	def test_convert_note_to_num2(self):
		self.assertEqual(DrumMap.split_note_octave("C0"), ("C", "0"))
		self.assertEqual(DrumMap.split_note_octave("C#10"), ("C#", "10"))
		self.assertEqual(DrumMap.split_note_octave("C#-2"), ("C#", "-2"))
		self.assertEqual(DrumMap.split_note_octave("E4"), ("E", "4"))
		self.assertEqual(DrumMap.split_note_octave("F#3"), ("F#", "3"))

	def test_convert_note_to_num(self):
		self.assertEqual(DrumMap.convert_note_to_num("C-2", False), 0)
		self.assertEqual(DrumMap.convert_note_to_num("C0", False), 24)
		self.assertEqual(DrumMap.convert_note_to_num("C#0", False), 25)
		self.assertEqual(DrumMap.convert_note_to_num("C1", False), 36)
		self.assertEqual(DrumMap.convert_note_to_num("C10", False), 144)
		self.assertEqual(DrumMap.convert_note_to_num("C#10", False), 145)

	def test_convert_middle_c(self):
		self.assertEqual(DrumMap.convert_note_to_num("C4", True), 60)  # Roland
		self.assertEqual(DrumMap.convert_note_to_num("C3", True), 48)  # Roland, AKAI
		self.assertEqual(DrumMap.convert_note_to_num("C2", True), 36)  # Roland, AKAI
		self.assertEqual(DrumMap.convert_note_to_num("C3", False), 60)  # Yamaha, Cubase
		self.assertEqual(DrumMap.convert_note_to_num("C4", False), 72)  # Yamaha, Cubase
		self.assertEqual(DrumMap.convert_note_to_num("C1", False), 36)  # Yamaha, Cubase

	def test_get_file_name_without_extension(self):
		self.assertEqual(DrumMap.get_file_name_without_extension("C:\\Music\\CubaseDrumMap\\Studio Drummer\\NI SD Garage Full 2.drm"), "NI SD Garage Full 2")
		self.assertEqual(DrumMap.get_file_name_without_extension("NI SD Garage Full 2.drm"), "NI SD Garage Full 2")

if __name__ == '__main__':
	unittest.main()
{% endhighlight %}