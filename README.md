# Bioassay-Experiment
Bioassay experiment is about survival test. For example, we have several dose of a treatment and randomly selected groups of experiment units. Then we record the number of death and build the model.

#### 1. First, estimate the parameters α and β using ML.
    bio.x = c(-0.86, -0.3, -0.05, 0.73)
    bio.n = c(    5,    5,     5,    5)
    bio.y = c(    0,    1,     3,    5)
    bio.data = cbind(bio.x, bio.n, bio.y)
    print(bio.data)
    response = cbind(bio.y, bio.n - bio.y)
    results = glm(response ~ bio.x, family = binomial)
    summary(results)
#### output:
         bio.x bio.n bio.y
    [1,] -0.86     5     0
    [2,] -0.30     5     1
    [3,] -0.05     5     3
    [4,]  0.73     5     5

    Call:
    glm(formula = response ~ bio.x, family = binomial)

    Deviance Residuals: 
           1         2         3         4  
    -0.17236   0.08133  -0.05869   0.12237  

    Coefficients:
                Estimate Std. Error z value Pr(>|z|)
    (Intercept)   0.8466     1.0191   0.831    0.406
    bio.x         7.7488     4.8728   1.590    0.112

    (Dispersion parameter for binomial family taken to be 1)

        Null deviance: 15.791412  on 3  degrees of freedom
    Residual deviance:  0.054742  on 2  degrees of freedom
    AIC: 7.9648

    Number of Fisher Scoring iterations: 7
    
#### 2. Now become a Bayesian. First we select the β priors that match our information about probability of death at a low and a high dose. We use −0:7 and 0:6 as the low and the high doses, respectively. At the low dose, we guess that the median probability of death is 0.2 and the 90th percentile of the probability of death is 0.5. At the high dose, the prior median and 90th percentile are 0.9 and 0.98, respectively.
    beta.select(list(p=.5,x=.2),list(p=.9,x=.5))
    beta.select(list(p=.5,x=.8),list(p=.9,x=.98))
#### output:
    [1] 1.12 3.56
    [1] 2.10 0.74
    
#### 3. Use the LearnBayes function logisticpost to obtain the posterior distribution of α and β. Draw the contours of the joint posterior and overlay 1000 draws from the posterior. The draws can be obtained by using the function simcontour. Here we use the extreme estimates as new data.
    bio.prior=rbind(c(-0.7, 4.68, 1.12),
                    c( 0.6, 2.84, 2.10))
    bio.data.new = rbind(bio.data, bio.prior)
    print(bio.data.new)
#### output:
         bio.x bio.n bio.y
    [1,] -0.86  5.00  0.00
    [2,] -0.30  5.00  1.00
    [3,] -0.05  5.00  3.00
    [4,]  0.73  5.00  5.00
    [5,] -0.70  4.68  1.12
    [6,]  0.60  2.84  2.10
    
#### 4. Use the LearnBayes function logisticpost to obtain the posterior distribution of α and β. Draw the contours of the joint posterior and overlay 1000 draws from the posterior. The draws can be obtained by using the function simcontour.
    mycontour(logisticpost,
              c(-3,3,-1,9),
              bio.data.new,
              xlab="beta0", 
              ylab="beta1")

    s=simcontour(logisticpost,
                 c(-2,3,-1,11),
                 bio.data.new,
                 1000)
    points(s)
    plot(density(s$y),xlab="beta1")
    plot(density(s$y),xlab="beta1")
    theta=-s$x/s$y
    hist(theta,xlab="LD-50",breaks=20)
    quantile(theta,c(.025,.975))

