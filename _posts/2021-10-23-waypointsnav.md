---
title: Navegação por waypoints + código comentado
date: 2021-10-23 14:07:00 +/-TTTT
categories: [Robótica, Robótica móvel]
tags: [robotica, ROS, Gazebo, navegacao]     # TAG names should always be lowercase
image:
  src: /assets/img/waypoints.png
  width: 400   # in pixels
  height: 400   # in pixels
  alt: waypointsnav
---

Este conteúdo é ideal para quem está começando em ROS e por isto, além do video de demonstração estou compartilhando também o código que criei, com comentários para simplificar a compreensão. Este código refere-se à navegação entre pontos no mapa, primeiramente adicionando  estes à uma lista e depois utilizando o movebase do Turtlebot para fazer o robô se mover por eles, sempre esperando chegar em um ponto antes de ir para o outro. A partir deste código pode-se desenvolver algoritmos de navegação mais complexos.

## Vídeo:
<iframe width="560" height="315" src="https://www.youtube.com/embed/V8V8sOzXaZE" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## Código comentado:
```python
#!/usr/bin/env python3
import rospy
from geometry_msgs.msg import PoseStamped
from geometry_msgs.msg import Twist, Vector3
from move_base_msgs.msg import MoveBaseActionGoal, MoveBaseAction, MoveBaseGoal, MoveBaseActionResult

class Turtlebot:
  def __init__(self):
    # lista que vai receber os waypoints:
    self.waypoints = []
    # iniciacao do node waypoints nav:
    rospy.init_node('waypointsnav')
    # instancia do objeto que vai publicar no topico move base:
    self.move_base_pub = rospy.Publisher("/move_base_simple/goal", PoseStamped, queue_size=1)

  # metodo para adicionar novos pontos na lista:
  def add_waypoint(self, list):
    self.waypoints.append(list)

  # publicar a posicao no move base, levando em consideracao a coordenada (0,0) do mapa:
  def goal_move_base(self, pose_x, pose_y, pose_z, pose_w):
    msg_move_to_goal = PoseStamped()
    msg_move_to_goal.pose.position.x = pose_x 
    msg_move_to_goal.pose.position.y = pose_y
    msg_move_to_goal.pose.orientation.z = pose_z
    msg_move_to_goal.pose.orientation.w = pose_w
    msg_move_to_goal.header.frame_id = 'map'

    rospy.sleep(1)

    self.move_base_pub.publish(msg_move_to_goal)
    
  # navegar entre os pontos, esperando chegar em um ponto antes de ir para o outro:
  def nav_into_points(self):
    for i in range(len(self.waypoints)):
      self.goal_move_base(self.waypoints[i][0], self.waypoints[i][1], self.waypoints[i][2], self.waypoints[i][3])
      rospy.wait_for_message("/move_base/result", MoveBaseActionResult, timeout=None)

if __name__ == '__main__':
  robot = Turtlebot()  
  
  robot.add_waypoint([-6.1,-1.0,-0.7,0.7])
  robot.add_waypoint([1.1,3.4,-0.7,0.7])
  robot.add_waypoint([6.2,-0.9,-0.7,0.7])
  
  robot.nav_into_points()
  
```

## Como executar:
O pacote encontra-se [neste repositório](https://github.com/leonlime/turtlenav) e para executar a navegação basta clonar o pacote para o seu workspace e seguir os comandos abaixo:

```roslaunch turtlenav turtlenav_gazebo.launch```

```rosrun turtlenav waypoints_nav.py```

