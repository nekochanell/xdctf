all:
	g++ -std=c++14 main.cpp -O0 -o tooslow

nosleep:
	gcc -o libnosleep.so -shared nosleep.c -fpic

solve:
	LD_PRELOAD=./libnosleep.so ./tooslow