{
 "cells": [
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "#One neuron to detect healthy or poison plants\n",
    "Author : NIERDING Axel on 7/5/22\n",
    "It's derived from MachineLearnia formation (big thanks to him)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 96,
   "metadata": {},
   "outputs": [],
   "source": [
    "\n",
    "import numpy as np #Numpy provides multidimensional array object, various derived objects (matrices, masked arrays,...) for fast operations on arrays, simulation, etc.\n",
    "import matplotlib.pyplot as plt #It's a collection of functions to createas plotting area in figure, plots some lines in plotting area, decorate the plot... and work like MATLAB\n",
    "from sklearn.datasets import make_blobs #Generate isotropic gaussian blobs for clustering"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "#Generate some data : it's some plants seperate by colors ; green = healthy ; yellow = poison"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 97,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "dimension de X: (100, 2)\n",
      "dimension de y: (100, 1)\n"
     ]
    },
    {
     "data": {
      "image/png": "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",
      "text/plain": [
       "<Figure size 432x288 with 1 Axes>"
      ]
     },
     "metadata": {
      "needs_background": "light"
     },
     "output_type": "display_data"
    }
   ],
   "source": [
    "X, y = make_blobs (n_samples = 100, n_features = 2, centers = 2, random_state=0) #Number of points equally divided among clusters ; number of features for each sample ; number of centers to generate ; Determines random number generation for dataset creation\n",
    "y = y.reshape((y.shape[0], 1)) \n",
    "\n",
    "print('dimension de X:', X.shape)\n",
    "print('dimension de y:', y.shape)\n",
    "\n",
    "plt.scatter(X[:,0], X[:, 1], c=y, cmap='summer')#Scatter plot \n",
    "plt.show()#Display plot"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "#Create one neuron with the same pattern as shown below\n",
    "\n",
    "![](Model.png)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "#Create the initialisation function"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 98,
   "metadata": {},
   "outputs": [],
   "source": [
    "def initialisation (X):\n",
    "    W = np.random.randn(X.shape[1], 1) #We shpae X matrix into vector\n",
    "    b = np.random.randn(1) #b is a real number as the model below\n",
    "    return (W , b)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "![](Xmatrix.png)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "#Create the model (X, W, b) "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 99,
   "metadata": {},
   "outputs": [],
   "source": [
    "def model(X, W, b):\n",
    "    Z = X.dot(W) + b\n",
    "    A = 1 / (1 + np.exp(-Z))\n",
    "    return A"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "![](AandZ.png)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "#Test the model"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 100,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "(100, 1)"
      ]
     },
     "execution_count": 100,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "A = model(X, W, b)\n",
    "A.shape\n",
    "#It's ok"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "#Create cost function"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 101,
   "metadata": {},
   "outputs": [],
   "source": [
    "def log_loss(A, y):\n",
    "    return 1/len(y) * np.sum(-y * np.log(A) - (1-y) * np.log(1 - A)) #minus is directly into the sum here (but it's the same as the equation below)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "![](Cost.png)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "#Test the cost function "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 102,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "0.2685254416046395"
      ]
     },
     "execution_count": 102,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "log_loss(A, y)\n",
    "#It's ok"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "#Create gradients function (A, X, y)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 103,
   "metadata": {},
   "outputs": [],
   "source": [
    "def gradients(A, X, y):\n",
    "    dW = 1 / len(y) * np.dot(X.T, A - y)\n",
    "    db = 1 / len(y) * np.sum(A - y)\n",
    "    return(dW, db)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "![](Gradient.png)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Test the gradients"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 104,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "(2, 1)"
      ]
     },
     "execution_count": 104,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "dW, db = gradients(A, X, y)\n",
    "dW.shape\n",
    "#It's ok "
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "#Create an update function with Gradients descending and W, b"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 105,
   "metadata": {},
   "outputs": [],
   "source": [
    "def update(dW, db, W, b, learning_rate):\n",
    "    W = W - learning_rate * dW\n",
    "    b = b - learning_rate * db\n",
    "    return(W, b)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "![](Update.png)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "#Define a predict function based on sigmoid model"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 106,
   "metadata": {},
   "outputs": [],
   "source": [
    "def predict(X, W, b):\n",
    "    A = model(X, W, b)\n",
    "    print(A)\n",
    "    return A >= 0.5 #-> True if above or False (i.e Toxic or Healthy)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "![](Sigmoid.png)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 107,
   "metadata": {},
   "outputs": [],
   "source": [
    "from sklearn.metrics import accuracy_score"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "#Compute the algorithm with all previous functions in One neuron"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 108,
   "metadata": {},
   "outputs": [],
   "source": [
    "def artificial_neuron(X, y, learning_rate = 0.1, n_iter = 100):\n",
    "    #initiation W, b\n",
    "    W, b = initialisation(X)#The initilisation function\n",
    "\n",
    "    \n",
    "    Loss = []#Empty list which growing during learning\n",
    "\n",
    "    for i in range(n_iter):# Learning loop (100 iterations here)\n",
    "        A = model(X, W, b)# Result of Model\n",
    "        Loss.append(log_loss(A, y))#Cost function\n",
    "        dW, db = gradients(A, X, y)#Descending gradients functions\n",
    "        W, b = update(dW, db, W, b, learning_rate)#Update (W,b)\n",
    "        \n",
    "    y_pred = predict(X, W, b)\n",
    "    print(accuracy_score(y, y_pred))#Print the accuracy to compare y with y pred\n",
    "\n",
    "    plt.plot(Loss)#Plot the list Loss to visualise evolution of cost\n",
    "    plt.show()#Display figure\n",
    "\n",
    "    return (W,b)\n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "![](ArtificialNeuron.png)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "#Show the learning curve and accuracy of model"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 109,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "[[9.81716576e-01]\n",
      " [6.57536120e-01]\n",
      " [4.32139014e-03]\n",
      " [8.96013600e-02]\n",
      " [9.69688460e-01]\n",
      " [3.32931557e-01]\n",
      " [7.01066015e-02]\n",
      " [9.70322057e-01]\n",
      " [4.18919849e-02]\n",
      " [8.02995493e-01]\n",
      " [2.93651781e-02]\n",
      " [8.37435456e-01]\n",
      " [3.15101305e-02]\n",
      " [1.41592113e-02]\n",
      " [6.94292609e-01]\n",
      " [9.90806084e-01]\n",
      " [9.90764115e-01]\n",
      " [2.91463609e-02]\n",
      " [6.03687487e-01]\n",
      " [5.76856412e-01]\n",
      " [4.98435229e-02]\n",
      " [3.38957063e-02]\n",
      " [3.58868874e-01]\n",
      " [4.39508887e-03]\n",
      " [9.48821371e-01]\n",
      " [3.33515858e-02]\n",
      " [8.34943247e-01]\n",
      " [1.12315786e-02]\n",
      " [7.79015009e-02]\n",
      " [7.65743386e-01]\n",
      " [9.72451105e-01]\n",
      " [4.69615160e-02]\n",
      " [5.64727791e-01]\n",
      " [9.73075695e-01]\n",
      " [3.99529355e-01]\n",
      " [2.91996837e-01]\n",
      " [7.16447192e-01]\n",
      " [5.67534741e-01]\n",
      " [5.09633422e-01]\n",
      " [3.35543117e-01]\n",
      " [4.37954794e-02]\n",
      " [1.31202819e-01]\n",
      " [7.33747375e-04]\n",
      " [1.70209614e-01]\n",
      " [1.67055062e-01]\n",
      " [8.36293654e-01]\n",
      " [9.52661023e-01]\n",
      " [9.38248379e-01]\n",
      " [3.28622054e-03]\n",
      " [1.01977496e-02]\n",
      " [9.19999977e-01]\n",
      " [4.44286573e-01]\n",
      " [8.66618864e-02]\n",
      " [4.97065434e-02]\n",
      " [8.74936153e-01]\n",
      " [4.68791322e-02]\n",
      " [7.79675963e-01]\n",
      " [7.80701387e-01]\n",
      " [9.55275277e-01]\n",
      " [9.87957126e-01]\n",
      " [5.94804161e-01]\n",
      " [2.21541397e-01]\n",
      " [4.39032728e-03]\n",
      " [9.31147488e-01]\n",
      " [1.85698107e-02]\n",
      " [3.59634991e-01]\n",
      " [2.65959261e-02]\n",
      " [7.09896154e-01]\n",
      " [9.53333689e-01]\n",
      " [3.02622581e-01]\n",
      " [1.73540785e-01]\n",
      " [8.85549052e-01]\n",
      " [9.80912265e-01]\n",
      " [1.35771537e-01]\n",
      " [7.99213457e-02]\n",
      " [3.09050149e-02]\n",
      " [4.45008222e-01]\n",
      " [8.84613675e-01]\n",
      " [3.96396877e-02]\n",
      " [3.66052229e-02]\n",
      " [2.48673632e-01]\n",
      " [1.00950664e-01]\n",
      " [3.52125870e-03]\n",
      " [1.42345562e-01]\n",
      " [7.60767016e-02]\n",
      " [1.05856736e-02]\n",
      " [9.71884269e-01]\n",
      " [1.09394888e-01]\n",
      " [7.43764118e-01]\n",
      " [9.54106238e-01]\n",
      " [9.14226957e-01]\n",
      " [9.79552037e-01]\n",
      " [3.90790332e-01]\n",
      " [8.70584810e-01]\n",
      " [4.04343909e-01]\n",
      " [6.87197126e-04]\n",
      " [9.55884770e-01]\n",
      " [9.65109276e-01]\n",
      " [6.30333036e-03]\n",
      " [3.40623176e-01]]\n",
      "0.86\n"
     ]
    },
    {
     "data": {
      "image/png": "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",
      "text/plain": [
       "<Figure size 432x288 with 1 Axes>"
      ]
     },
     "metadata": {
      "needs_background": "light"
     },
     "output_type": "display_data"
    }
   ],
   "source": [
    "W, b = artificial_neuron(X, y)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "#The model accuracy is 86%"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Example : "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 110,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "image/png": "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",
      "text/plain": [
       "<Figure size 432x288 with 1 Axes>"
      ]
     },
     "metadata": {
      "needs_background": "light"
     },
     "output_type": "display_data"
    },
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "[0.85733932]\n"
     ]
    },
    {
     "data": {
      "text/plain": [
       "array([ True])"
      ]
     },
     "execution_count": 110,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "new_plant = np.array([2, 1])# Choose one plant here 2,1 (toxic)\n",
    "\n",
    "\n",
    "plt.scatter(X[:,0], X[:, 1], c=y, cmap='summer')#Scatter plot of all plants\n",
    "plt.scatter(new_plant[0], new_plant[1], c='r')#The plant choosen in red\n",
    "\n",
    "plt.show()\n",
    "predict(new_plant, W, b)#Test the model"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "#True -> Toxic, so the model is great with ~86% of accuracy\n",
    "#But what is the decision line ? "
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "![](Equation.png)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 111,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "image/png": "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",
      "text/plain": [
       "<Figure size 432x288 with 1 Axes>"
      ]
     },
     "metadata": {
      "needs_background": "light"
     },
     "output_type": "display_data"
    },
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "[0.85733932]\n"
     ]
    },
    {
     "data": {
      "text/plain": [
       "array([ True])"
      ]
     },
     "execution_count": 111,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "new_plant = np.array([2, 1])\n",
    "\n",
    "x0 = np.linspace(-1, 4, 100)# -1 to 4 with 100 data\n",
    "x1 = (-W[0] * x0 -b) / W[1]# W[0] is the first value of W\n",
    "\n",
    "plt.scatter(X[:,0], X[:, 1], c=y, cmap='summer')\n",
    "plt.scatter(new_plant[0], new_plant[1], c='r')\n",
    "plt.plot(x0, x1, c='orange', lw=3) #Plot the decision line\n",
    "plt.show()\n",
    "predict(new_plant, W, b)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "![](Decision.png)"
   ]
  }
 ],
 "metadata": {
  "interpreter": {
   "hash": "01a33d69adc33097d0bae5bdbf282579fb884f40eff9531eed4ace90b76278af"
  },
  "kernelspec": {
   "display_name": "Python 3.9.7 ('base')",
   "language": "python",
   "name": "python3"
  },
  "language_info": {
   "codemirror_mode": {
    "name": "ipython",
    "version": 3
   },
   "file_extension": ".py",
   "mimetype": "text/x-python",
   "name": "python",
   "nbconvert_exporter": "python",
   "pygments_lexer": "ipython3",
   "version": "3.9.7"
  },
  "orig_nbformat": 4
 },
 "nbformat": 4,
 "nbformat_minor": 2
}
