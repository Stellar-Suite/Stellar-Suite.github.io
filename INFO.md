# Project Info
The Stellar Suite is a funny name used to refer to the 3 subprojects: Astral, Stargate, Hyperwarp. These projects work together to provide a complete remote streaming experience. Astral (a vite react app) serves as a frontend to Stargate (a very simple express backend) which then launches applications with Hyperwarp injected into them. Essentially in more detail...

## Astral
Minimal application launcher ui, to be worked on later. Has not changed since Summer of 2023. Acts as a client to a Stargate server you input in, takes inspiration from a certain app.
![image](https://github.com/Stellar-Suite/Stellar-Suite.github.io/assets/20248577/2331b491-39b9-427f-a913-30716a0083d8)
![image](https://github.com/Stellar-Suite/Stellar-Suite.github.io/assets/20248577/67db8559-f2d8-48a9-a948-350626f5d403)
![image](https://github.com/Stellar-Suite/Stellar-Suite.github.io/assets/20248577/d8c3af4d-d58a-4184-905f-b9342ab00cab)


## Stargate
This is the backend that manages launching applications (mostly random foss games). It takes care of attempting to isolate user data (not really) and is highly configurable via a single `config.toml` to provide whatever applications you need, It provides authentication, the launchable applications, and the notion of sessions to the Astral frontend. Here is an example of my development config file:

```toml
users = [
    {id = "testing", password = "password"}
]
secret = 'blah'
managementOptions.hyperwarpPath = "/run/media/raymond/Data/Coding/Hyperwarp/hyperwarp"
managementOptions.hyperwarpTarget = "debug"
managementOptions.streamerPath = "/run/media/raymond/Data/Coding/Hyperwarp/streamerd/target/debug/streamerd"
debug = 1

[[appSpecs]]
displayName = 'Supertuxkart'
binary = 'supertuxkart'
id = 'stk'
poster = "/assets/stk.png"
background = "/assets/stk_bg.jpg"
description = '''
Karts. Nitro. Action! SuperTuxKart is a 3D open-source arcade racer with a variety of characters, tracks, and modes to play. Our aim is to create a game that is more fun than realistic, and provide an enjoyable experience for all ages.

In Story mode, you must face the evil Nolok, and defeat him in order to make the Mascot Kingdom safe once again! You can race by yourself against the computer, compete in several Grand Prix cups, or try to beat your fastest time in Time Trial mode. You can also race, battle or play soccer with up to eight friends on a single computer, play on a local network or play online with other players all over the world.
'''

[[appSpecs]]
displayName = 'Open Transport Tycoon'
binary = 'openttd'
id = 'openttd'
poster = "/assets/openttd.jpg"
background = "/assets/openttd_bg.png"
description = '''
OpenTTD is a business simulation game in which players try to earn money by transporting passengers, minerals and goods via road, rail, water and air. It is an open-source remake and expansion of the 1995 Chris Sawyer video game Transport Tycoon Deluxe. 

OpenTTD duplicates most features of Transport Tycoon Deluxe and has many additions, including a range of map sizes, support for many languages, custom (user-made) artificial intelligence (AI), downloadable customisations, ports for several widely used operating systems, and a more user-friendly interface. OpenTTD also supports local area network (LAN) and Internet multiplayer, co-operative and competitive, for up to 255 players.
'''

[[appSpecs]]
displayName = 'glxgears'
binary = 'glxgears'
id = 'glxgears'
description = '''
Testing application
'''
```

## Hyperwarp
Most of the focus for the past few weeks has been here. It's the most time consuming, nontrivial, and cursed part of the project. 
Hyperwarp is a big Rust based `LD_PRELOAD` library that invasively modifies (good luck running this on a game with anticheat) the runtime behavior of any binary it is injected to. 
A variety of things are hooked into like swapbuffers and it currently supports OpenGL only but at this point in time it's "capture" feature capability "works" as a MVP but there are some caveats (performance possibly) that will be listed in the Hyperwarp docs soon
Oh yea it's also configured with environment variables that are meant to be controlled by Stargate, will also be documented.

### streamerd
Hyperwarp's repository has more than one crate. Hyperwarp by itself can't stream it's framebuffer by design, the most it can do is provide a unix socket or similar to control the process as well as the capture mode which provides the current framebuffer as a raw image in `/dev/shm` (this will def change with future capture methods). There is a seperate binary crate called `streamerd` (streamer daemon) that connects to that unix socket and feeds the frames into Gstreamer (which has proven to work before based off a [poc of abusing it to play minecraft remotely](https://github.com/javaarchive/MineWarp) ), but currently it only goes to the `autovideosink` element for testing. They share a crate called `stellar_protocol` to share protocol definitions and standardized serialzie/deserialize functions.

At this point in time, all 4 projects work together to display supertuxkart's framebuffer in autovideosink to an acceptable degree. Current work is being done mostly on Hyperwarp still.

# Repostories
I am in the process of moving the repos out of my github account with a hell lot of random things into this github org but I'm trying to figure out how to move them without breaking my current git remotes.

# Inspiration
* [Selkies Gstreamer](https://github.com/selkies-project/selkies-gstreamer) - state of the art open source webrtc over gstreamer.
* [Netris](https://github.com/netrisdotme/netris) - Takes a new approach and focuses more towards running games.
* [Neko](https://github.com/m1k1o/neko) - a sharable virtual desktop over webrtc using go's pion
