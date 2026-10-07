extends Control

@export var max_radius = 100.0
@export var stick_radius = 40.0

var center = Vector2.ZERO
var stick_pos = Vector2.ZERO
var active_touch = -1
var direction = Vector2.ZERO

func _ready():
	center = size / 2
	stick_pos = center

func _draw():
	draw_circle(center, max_radius, Color(1, 1, 1, 0.25))
	draw_arc(center, max_radius, 0, TAU, 48, Color(1, 1, 1, 0.7), 3.0)
	draw_circle(stick_pos, stick_radius, Color(1, 1, 1, 0.6))
	draw_arc(stick_pos, stick_radius, 0, TAU, 48, Color(1, 1, 1, 0.9), 3.0)

func _gui_input(event):
	if event is InputEventScreenTouch:
		if event.pressed and active_touch == -1:
			active_touch = event.index
			_update_stick(event.position)
		elif not event.pressed and event.index == active_touch:
			active_touch = -1
			stick_pos = center
			direction = Vector2.ZERO
			queue_redraw()
	elif event is InputEventScreenDrag:
		if event.index == active_touch:
			_update_stick(event.position)

func _update_stick(pos):
	var offset = pos - center
	if offset.length() > max_radius:
		offset = offset.normalized() * max_radius
	stick_pos = center + offset
	direction = offset / max_radius
	queue_redraw()
