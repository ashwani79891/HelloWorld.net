##comment out
import mediapipe as mp
import cv2
import numpy as np
from mediapipe.framework.formats import landmark_pb2
import time
from math import sqrt
import win32api
import pyautogui
 
 
 
mp_drawing = mp.solutions.drawing_utils
mp_hands = mp.solutions.hands
click=0
 
video = cv2.VideoCapture(0)
 
with mp_hands.Hands(min_detection_confidence=0.8, min_tracking_confidence=0.8) as hands: 
    while video.isOpened():
        _, frame = video.read()
        image = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)
         
        image = cv2.flip(image, 1)
 
        imageHeight, imageWidth, _ = image.shape
 
        results = hands.process(image)
   
 
        image = cv2.cvtColor(image, cv2.COLOR_RGB2BGR)
  
        if results.multi_hand_landmarks:
            for num, hand in enumerate(results.multi_hand_landmarks):
                mp_drawing.draw_landmarks(image, hand, mp_hands.HAND_CONNECTIONS, 
                                        mp_drawing.DrawingSpec(color=(250, 44, 250), thickness=2, circle_radius=2),
                                         )
 
        if results.multi_hand_landmarks != None:
          for handLandmarks in results.multi_hand_landmarks:
            for point in mp_hands.HandLandmark:
 
    
                normalizedLandmark = handLandmarks.landmark[point]
                pixelCoordinatesLandmark = mp_drawing._normalized_to_pixel_coordinates(normalizedLandmark.x, normalizedLandmark.y, imageWidth, imageHeight)
    
                point=str(point)
 
                if point=='HandLandmark.INDEX_FINGER_TIP':
                 try:
                    indexfingertip_x=pixelCoordinatesLandmark[0]
                    indexfingertip_y=pixelCoordinatesLandmark[1]
                    win32api.SetCursorPos((indexfingertip_x*4,indexfingertip_y*5))
 
                 except:
                    pass
 
                elif point=='HandLandmark.THUMB_TIP':
                 try:
                    thumbfingertip_x=pixelCoordinatesLandmark[0]
                    thumbfingertip_y=pixelCoordinatesLandmark[1]
                    #print("thumb",thumbfingertip_x)
 
                 except:
                    pass
 
                try:
                    #pyautogui.moveTo(indexfingertip_x,indexfingertip_y)
                    Distance_x= sqrt((indexfingertip_x-thumbfingertip_x)**2 + (indexfingertip_x-thumbfingertip_x)**2)
                    Distance_y= sqrt((indexfingertip_y-thumbfingertip_y)**2 + (indexfingertip_y-thumbfingertip_y)**2)
                    if Distance_x<5 or Distance_x<-5:
                        if Distance_y<5 or Distance_y<-5:
                            click=click+1
                            if click%5==0:
                                print("single click")
                                pyautogui.click()                            
 
                except:
                    pass
 
        cv2.imshow('Hand Tracking', image)
 
        if cv2.waitKey(10) & 0xFF == ord('q'):
            break
 
video.release()

Best First Search:

class Node:
    def __init__(self, v, weight):
        self.v=v
        self.weight=weight

# pathNode class will help to store
# the path from src to dest.
class pathNode:
    def __init__(self, node, parent):
        self.node=node
        self.parent=parent

# Function to add edge in the graph.
def addEdge(u, v, weight):
    # Add edge u -> v with weight weight.
    adj[u].append(Node(v, weight))


# Declaring the adjacency list
adj = []
# Greedy best first search algorithm function
def GBFS(h, V, src, dest):
    """ 
    This function returns a list of 
    integers that denote the shortest
    path found using the GBFS algorithm.
    If no path exists from src to dest, we will return an empty list.
    """
    # Initializing openList and closeList.
    openList = []
    closeList = []

    # Inserting src in openList.
    openList.append(pathNode(src, None))

    # Iterating while the openList 
    # is not empty.
    while (openList):

        currentNode = openList[0]
        currentIndex = 0
        # Finding the node with the least 'h' value
        for i in range(len(openList)):
            if(h[openList[i].node] < h[currentNode.node]):
                currentNode = openList[i]
                currentIndex = i

        # Removing the currentNode from 
        # the openList and adding it in 
        # the closeList.
        openList.pop(currentIndex)
        closeList.append(currentNode)
        
        # If we have reached the destination node.
        if(currentNode.node == dest):
            # Initializing the 'path' list. 
            path = []
            cur = currentNode

            # Adding all the nodes in the 
            # path list through which we have
            # reached to dest.
            while(cur != None):
                path.append(cur.node)
                cur = cur.parent
            

            # Reversing the path, because
            # currently it denotes path
            # from dest to src.
            path.reverse()
            return path
        

        # Iterating over adjacents of 'currentNode'
        # and adding them to openList if 
        # they are neither in openList or closeList.
        for node in adj[currentNode.node]:
            for x in openList:
                if(x.node == node.v):
                    continue
            
            for x in closeList:
                if(x.node == node.v):
                    continue
            
            openList.append(pathNode(node.v, currentNode))

    return []

# Driver Code
""" Making the following graph
             src = 0
            / | \
           /  |  \
          1   2   3
         /\   |   /\
        /  \  |  /  \
        4   5 6 7    8
               /
              /
            dest = 9
"""
# The total number of vertices.
V = 10
## Initializing the adjacency list
for i in range(V):
    adj.append([])

addEdge(0, 1, 2)
addEdge(0, 2, 1)
addEdge(0, 3, 10)
addEdge(1, 4, 3)
addEdge(1, 5, 2)
addEdge(2, 6, 9)
addEdge(3, 7, 5)
addEdge(3, 8, 2)
addEdge(7, 9, 5)

# Defining the heuristic values for each node.
h = [20, 22, 21, 10, 25, 24, 30, 5, 12, 0]
path = GBFS(h, V, 0, 9)
for i in range(len(path) - 1):
    print(path[i], end = " -> ")

print(path[(len(path)-1)])
