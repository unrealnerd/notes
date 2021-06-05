# Notes on ML

- [Learn ASL](http://asl.ms/)
- [How Mediapipe works to getHandsRegion->detectPoints->gestureRecognize](https://ai.googleblog.com/2019/08/on-device-real-time-hand-tracking-with.html)
- Mediapipe flows these steps
  - BlazePalm recognizes palm in image boundry so that the analysis can be only the palm area.
  - Hand landmark then run to find and track the key hand points
  - Gesture recogizer then classifies the image to pattern
- Directed graph: is a graph that is made up of a set of vertices connected by edges, where the edges have a direction associated with them.
- Palm detector is only when the cofidence of palm is gone down.
- 

- types of learning supervised, unsupervised, semi-supervised, reinforced
- supervised
  - classification - classifing a point to a particular label
  - regression - output is a number. eg: todays temp could be 
- unsupervised
  - clustering - identifying patterns that are present in a data dump. unlike classification labels are unknown
- feature is input. label is output
- squared loss is the square of loss of each point to meet the ideal line
- y=mx+b y is the to be predicted value, m is the slope/weight of the line(line of best fit) drawn with minimum loss, x is feature which can be multiple y= b + w0+x0 + w1+x1...
- gradient descent alg - the convex curve to find the minimum loss with the least step
- learning rate is the step size @ which to learn. smaller the value slower. grater the val over shoots. goldilocks are the perfect learning rate
- stochastic(randomly) gradient desecent is randomly taking a single example of huge data set. mini-batch SGD is set of 100-1000 examples.
- reinforcement learning - rewarding the agent when it get closer to the objective in the defined environment
- epochs- no. of times the same data is fed to the model