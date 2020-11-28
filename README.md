# <b>LÊ THỊ TÂM - 18021118</b>

## Question 1 - Exact Inference Observation

Xác suất ở mỗi vị trí mà ma có thể xuất hiện được tính bằng tích xác suất hậu nghiệm của tiếng ồn dựa trên khoảng cách manhattan của pacman với ma (P(noisyDistance|trueDistance)) nhân với xác suất tiên nghiệm mà ma xuất hiện ở mỗi vị trí (được cho bởi phân phối đều)

```python
allPossible = util.Counter()
        if noisyDistance == None:
            allPossible = util.Counter()
            allPossible[self.getJailPosition()] = 1.0
        else:
            for location in self.legalPositions:
                distance = util.manhattanDistance(location, pacmanPosition)
                allPossible[location] = emissionModel[distance] * self.beliefs[location]
```
                
```python
python autograder.py -q q1
python autograder.py -q q1 --no-graphics
```

## Question 2 - Exact Inference with Time Elapse

Xác suất mà ma xuất hiện ở vị trí kế tiếp sẽ bằng xác suất ở vị trí hiện tại nhân với xác suất mà ma sẽ xuất hiện ở các vị trí tiếp theo(được cho bởi phân bố tùy vào loại ma). Hoặc có thể hiểu đây là xác suất có điều kiện P(vị trí tiếp theo | vị trí hiện tại của ma). Sau đó ta sẽ cập nhật xác suất này:

```python
   newBeliefs = util.Counter()
        for myPos in self.legalPositions:
            newPosDist = self.getPositionDistribution(self.setGhostPosition(gameState, myPos))
            oldBelief = self.beliefs[myPos]
            for newPos in newPosDist.keys():
                newBeliefs[newPos] += oldBelief * newPosDist[newPos]

        self.beliefs = newBeliefs
```

```python
python autograder.py -q q2
python autograder.py -q q2 --no-graphics
```

## Question 3 - Exact Inference Full Test

Tìm ra vị trí có khả năng xuất hiện nhất của mỗi ma và khoảng cách từ pacman đến những vị trí đó:

```python
 likelyGhostPositions = []
        for ghostPosDist in livingGhostPositionDistributions:
            maxProb = -1
            for pos in ghostPosDist.keys():
                if ghostPosDist[pos] > maxProb:
                    likelyPos = pos
                    maxProb = ghostPosDist[pos]
            likelyGhostPositions += [likelyPos]

        minDistance = float("inf")
        for ghostPos in likelyGhostPositions:
            if minDistance > self.distancer.getDistance(pacmanPosition, ghostPos):
                minDistance = self.distancer.getDistance(pacmanPosition, ghostPos)
                closeGhost = ghostPos

        for action in legal:
            if self.distancer.getDistance(Actions.getSuccessor(pacmanPosition, action), closeGhost) < self.distancer.getDistance(pacmanPosition, closeGhost):
                return action
```

```python
python autograder.py -q q3
```
```python
python autograder.py -q q3 --no-graphics
```

## Question 4 - Approximate Inference Observation

Hàm *initializeUniformly():* 
```python
def initializeUniformly(self, gameState):
        n = self.numParticles
        legalPos = self.legalPositions
        uniformInitialized = []
        while n > 0:
            if n > len(legalPos):
                uniformInitialized += legalPos
                n -= len(legalPos)
            else:
                uniformInitialized += legalPos[0:n-1]
                n = 0

        self.particles = uniformInitialized

```
Hàm *getBeliefDistribution():* Khởi tạo xác suất cho mỗi vị trí trong tập particles là bằng nhau và tổng bằng 1 (phân phối đều):

```python
   def getBeliefDistribution(self):
        distribution = util.Counter()
        for element in self.particles:
            distribution[element] += 1
        distribution.normalize()
        return distribution
```
Hàm *observe():* Tương tự hàm observe() của Question 1:

```python
 def observe(self, observation, gameState):
        noisyDistance = observation
        emissionModel = busters.getObservationDistribution(noisyDistance)
        pacmanPosition = gameState.getPacmanPosition()
        if noisyDistance == None:
            jailCounter = util.Counter()
            jailCounter[self.getJailPosition()] = 1.0
            self.beliefs = jailCounter
        else:
            tempCounter = util.Counter()
            for p in self.particles:
                trueDistance = util.manhattanDistance(p, pacmanPosition)
                tempCounter[p] += emissionModel[trueDistance]
            self.beliefs = tempCounter

        if self.beliefs.totalCount() == 0:
            self.initializeUniformly(gameState)
        else:
            self.beliefs.normalize()
            for i in range(len(self.particles)):
                newPos = util.sample(self.beliefs)
                self.particles[i] = newPos
```
                                                        
Khởi tạo lại tập mẫu nếu như xác suất ở tất cả các vị trí đều bằng 0:

```python
    if allPossible.totalCount() == 0:
        self.initializeUniformly(gameState)
```
```python
python autograder.py -q q4
python autograder.py -q q4 --no-graphics
```

## Question 5 - Approximate Inference with Time Elapse
```python
python autograder.py -q q5
python autograder.py -q q5 --no-graphics
```

## Question 6 - Joint Particle Filter Observation
```python
python autograder.py -q q6
python autograder.py -q q6 --no-graphics
```

## Question 7 - Joint Particle Filter with Elapse Time
```python
python autograder.py -q q7
python autograder.py -q q7 --no-graphics
```


