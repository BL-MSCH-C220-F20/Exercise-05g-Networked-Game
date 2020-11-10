# Exercise-05g-Networked-Game

Exercise for MSCH-C220, 10 November 2020

This exercise is a simple introduction to a multi-player game in Godot using the RPC framework. It is based on a tutorial provided by Garbaj, [3D Multiplayer for Beginners](https://www.youtube.com/watch?v=K0luHLZxjBA).

Fork this repository. When that process has completed, make sure that the top of the repository reads [your username]/Exercise-05g-Networked-Game. Edit the LICENSE and replace BL-MSCH-C220-F20 with your full name. Commit your changes.

Clone the repository to a Local Path on your computer. Open the project.godot file in Godot.

---

First, we need to set up the network connection. This will be done in the Lobby Scene. Open the Lobby.gd script and replace it with the following:

```
extends Node2D

func _ready():
	var _connect = get_tree().connect("network_peer_connected", self, "_player_connected")


func _on_Host_pressed():
	var net = NetworkedMultiplayerENet.new()
	net.create_server(13458, 4095)
	get_tree().set_network_peer(net)
	print("hosting")

func _on_Join_pressed():
	var net = NetworkedMultiplayerENet.new()
	net.create_client("127.0.0.1", 13458)
	get_tree().set_network_peer(net)

func _player_connected(id):
	Global.player2id = id
	var game = preload("res://Game.tscn").instance()
	get_tree().get_root().add_child(game)
	hide()
```

Now, we need to connect the buttons to the script. Connect pressed signals from the Host and Join buttons to the corresponding methods in the Lobby.gd script.

---

Next, we need to make the player able to transmit information about its position. In the Player.gd script, add the following method at line 7:
```
remote func _set_position(pos):
	global_transform.origin = pos
```

Then, we need to communicate the position of the player to the other running instance of the game.

Replace the move_and_slide() statement (which should now be on line 15) with the following:
```
		if is_network_master():
			move_and_slide(direction * speed, Vector3.UP)
		rpc_unreliable("_set_position", global_transform.origin)
```

This causes the player to move_and_slide only on the current player. It then transmits the position to the other instance.

---

Save and run the game. Press the Host button. Then open another copy of the project in another Godot window. Run it and press the Join button. You should now have two windows that are able to communicate with each other.

---

For extra credit, could you replace the capsule with our animated player character? Could you communicate the animations as well as the position? I have saved the player character in the Player2 folder. If you decide to implement the extra credit, indicate that in the README.md.

---

Quit Godot. In GitHub desktop, add a summary message, commit your changes and push them back to GitHub. If you return to and refresh your GitHub repository page, you should now see your updated files with the time when they were changed.

Now edit the README.md file. When you have finished editing, commit your changes, and then turn in the URL of the main repository page (https://github.com/[username]/Exercise-05g-Networked-Game) on Canvas.

The final state of the file should be as follows (replacing the "Created by" information with your name):

```
# Exercise-05g-Networked-Game
Exercise for MSCH-C220, 10 November 2020

A simple networked game

## Implementation
Built using Godot 3.2.2

## References
[3D Multiplayer for Beginners](https://www.youtube.com/watch?v=K0luHLZxjBA)
[Kenney.nl Animation Characters](https://kenney.nl/assets/animated-characters-2)

## Future Development
None

## Extra Credit
None

## Created by 
Jason Francis
```
