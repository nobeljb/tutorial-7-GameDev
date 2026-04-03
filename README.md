# Tutorial 7 Game Development

1. Membuat objek player, menyesuaikan kamera, lalu attach script agar player dapat bergerak
2. Lalu membuat 2 script yaitu Interactable dan Switch sesuai tutorial
3. Lalu attach script Switch ke StaticBody3d childnya switch. Lalu setting variabel lightnya ke OmniLight3D
4. Lalu menambahkan RayCast3D, dan menyeting arahnya agar sesuai dengan pandangan kamera, sekaligus attach script yang ada ditutorial pada RayCast3Dnya
5. Sehingga lampunya bisa hidup dan mati sesuai dengan tutorial
6. Membuat level 1 dengan CSG beserta obstaclenya sesuai dengan tutorial
7. Membuat objek lampu juga dan ditepatkan pada level 1, saya tambahkan lampu omni light juga, jadi ada cahaya dari objek lampu tersebut
8. Membuat Area Trigger, lalu attach script beserta signalnya agar dapat mendeteksi player
9. Memposisikan Area Trigger pada level 1 sehigga saat player jatuh maka akan kembali reload level 1
10. Merubah nama scene level yang telah disediakan menjadi Playground, nanti scene ini akan jadi Win Stage untuk player bisa memain2kan lampu
11. Mengubah project setting pada bagian Application -> Run -> Main Scene menjadi level 1
12. Memposisikan juga Area Trigger di level 1, jadi saat player sampai ke sisi ujung maka pindah ke Playground. Jadi kedua scene sekarang terhubung
13. Menambah mekanik Sprinting dan Crouching dengan mengubah script pada player menjadi seperti ini
```python
extends CharacterBody3D

@export var walk_speed: float = 5.0
@export var sprint_speed: float = 10.0
@export var crouch_speed: float = 2.5
@export var acceleration: float = 5.0
@export var gravity: float = 9.8
@export var jump_power: float = 5.0
@export var mouse_sensitivity: float = 0.3

@onready var camera: Camera3D = $Head/Camera3D
@onready var head: Node3D = $Head

var camera_x_rotation: float = 0.0
var current_speed: float = 0.0

func _ready():
	Input.set_mouse_mode(Input.MOUSE_MODE_CAPTURED)

func _input(event):
	if Input.is_action_just_pressed("ui_cancel"):
		Input.set_mouse_mode(Input.MOUSE_MODE_VISIBLE)

	if event is InputEventMouseMotion and Input.get_mouse_mode() == Input.MOUSE_MODE_CAPTURED:
		head.rotate_y(deg_to_rad(-event.relative.x * mouse_sensitivity))

		var x_delta = event.relative.y * mouse_sensitivity
		camera_x_rotation = clamp(camera_x_rotation + x_delta, -90.0, 90.0)
		camera.rotation_degrees.x = -camera_x_rotation

func _physics_process(delta):
	var movement_vector = Vector3.ZERO

	# INPUT GERAK
	if Input.is_action_pressed("movement_forward"):
		movement_vector -= head.basis.z
	if Input.is_action_pressed("movement_backward"):
		movement_vector += head.basis.z
	if Input.is_action_pressed("movement_left"):
		movement_vector -= head.basis.x
	if Input.is_action_pressed("movement_right"):
		movement_vector += head.basis.x

	movement_vector = movement_vector.normalized()

	if Input.is_action_pressed("crouch"):
		current_speed = crouch_speed
	elif Input.is_action_pressed("sprint"):
		current_speed = sprint_speed
	else:
		current_speed = walk_speed

	# APPLY MOVEMENT
	velocity.x = lerp(velocity.x, movement_vector.x * current_speed, acceleration * delta)
	velocity.z = lerp(velocity.z, movement_vector.z * current_speed, acceleration * delta)

	# GRAVITY
	if not is_on_floor():
		velocity.y -= gravity * delta

	# JUMP (disable saat crouch biar realistis)
	if Input.is_action_just_pressed("jump") and is_on_floor() and not Input.is_action_pressed("crouch"):
		velocity.y = jump_power

	move_and_slide()
```
14. Lalu menambahkan input map sprint dengan Shift dan crouch dengan ctrl pada project setting
15. Sehingga player dapat bergerak lebih cepat dengan sprint. Dapat juga crouch untuk bergerak secara perlahan dan mencegah lompat untuk pergerakan presisi.
