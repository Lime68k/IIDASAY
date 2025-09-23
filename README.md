# INTERNET IS DYING AND SO ARE YOU

- dependence : icecast mpd mpc yt-dlp caddy + u also need a static IP
- add basic info in icecast during install, hostname = your local IP
- add this mountpoint in /etc/mpd.conf:

		audio_output {
		    type        "shout"
		    name        "whatever"		
		    host        "yourLocalIP"	#same as icecast
		    port        "8000"		      
		    mount       "/mystream.mp3" 
		    password    "hackme"        #same as icecast      
		    encoder     "lame"                
		    bitrate     "128"                 
		    format      "44100:16:2"
		}

- set this as your music directiory in /etc/mpd.conf 

		music_directory         "~/Music"


- create dir ~/Music/webradio
- create dir /var/www/live-info give permission to your current user and caddy
- config caddy /etc/caddy/Caddyfile :

		radio.domain.public {
			handle_path /covers/* {
			 root * /var/www/live-info/covers
			 file_server
		          header {
		             Access-Control-Allow-Origin *
		             Access-Control-Allow-Methods "GET, OPTIONS"
		             Access-Control-Allow-Headers "Content-Type"
		             Cache-Control: no-cache, no-store, must-revalidate
		          }
			}
			handle_path /np/* {
			 root * /var/www/live-info/np
			 file_server
		          header {
		             Access-Control-Allow-Origin *
		             Access-Control-Allow-Methods "GET, OPTIONS"
		             Access-Control-Allow-Headers "Content-Type"
		             Cache-Control: no-cache, no-store, must-revalidate
		          }
			}
	        handle_path /dj/* {
  			  root * /var/www/live-info/dj
	          file_server
		          header {
		             Access-Control-Allow-Origin *
		             Access-Control-Allow-Methods "GET, OPTIONS"
		             Access-Control-Allow-Headers "Content-Type"
		             Cache-Control: no-cache, no-store, must-revalidate
		          }
	        }
			handle {
			 reverse_proxy yourLocalIP:8000
			}
		}

- in fetch-info.sh replace my domain with your domain, the same you put in caddy
	
		DOMAIN="radio.spaceghost.pink"  # ← REPLACE BY YOUR PUBLIC DOMAIN

- create a colaborative txt, and put the raw link into a file called ~/colab.txt
  
here is an exemple of a colaborative txt: 
	
		[morning]
		https://soundcloud.com/n2v2/sets/1
		https://soundcloud.com/louisaeae/sets/1
		https://youtube.com/playlist?list=JFOZNoinaofne
		
		[afternoon]
		https://soundcloud.com/n2v2/sets/2
		https://soundcloud.com/louisaeae/sets/2
		https://youtube.com/playlist?list=blablaFRRINe
		
		[evening]
		https://soundcloud.com/n2v2/sets/3
		https://soundcloud.com/louisaeae/sets/3
		https://youtube.com/playlist?list=bjqfnieuNFIEUNvjzzi
		
		[night]
		https://soundcloud.com/n2v2/sets/4
		https://soundcloud.com/louisaeae/sets/4
		https://youtube.com/playlist?list=fiIFJfiejF
		
		[after]
		https://soundcloud.com/n2v2/sets/5
		https://soundcloud.com/louisaeae/sets/5
		https://youtube.com/playlist?list=foiOFJfeio
		
		[sleep]
		https://soundcloud.com/n2v2/sets/6
		https://soundcloud.com/louisaeae/sets/6
		https://youtube.com/playlist?list=dijenfOFNIGeijgn

- create another colaborative txt, and put the raw link into a file called ~/colab2.txt
  
here is an exemple of the second colaborative txt: 

		[0]
		pseudo=
		reseaux=
		playlist=
		texte=
		
		[1]
		pseudo=névé
		reseaux=https://spaceghost.pink
		playlist=https://soundcloud.com/user-255544422233/t4t
		texte=voici un exemple de comment vous pouvez rediger votre bloc. evitez les charactères spéciaux notamment les guillement, ainsi que les sauts de ligne. 
		
		[2]
		pseudo=labtoxin
		reseaux=https://soundcloud.com/labtoxin
		playlist=https://soundcloud.com/hypercore-collective/hypercore-url-labtoxin
		texte=set pour tester l'update, j'espere que ca marche pcq c vraiment trop cool. ce texte est sencé etre posté dans un blux.

- configure type A DNS like radio.domain.public answer your public IP

u can now run update-playlists.sh and wait for your tracks to download then enable systemd for icecast, mpd, caddy then create and enable a service for radio-start.sh and fetch-info.sh

your stream should be available at https://radio.domain.public/mystream.mp3 (/mystream.mp3 being the mount point u choose in mpd)
you should find URL and cover of the current track at https://radio.domain.public/nowplaying.json

my index.html works for https://radio.spaceghost.pink but i guess u can just copy my js and change URL, just know that, the whole js is mostly vibe coded as i rly didnt wanted to learn javascript 

force refresh : nohup ~/refresh.sh > ~/refresh.log 2>&1 & disown

on debian, you need venv for the bluesky bot, install it and create a virtual environment like 

	cd ~/bluesky-bot
	python3 -m venv .venv

you also need a systemd service for np-bot $HOME/bluesky-bot/.venv/bin/python $HOME/bluesky-bot/np-bot.py
