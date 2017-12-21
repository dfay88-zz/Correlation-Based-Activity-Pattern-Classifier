# Comparison of logit models to machine learning algorithms for modeling individual daily activity pattern types
Daniel Fay, Gaurav Vyas, Peter Vovsha (WSP)  
### Abstract  
Travel models and associated discrete choice structures until today have been dominated by logit modeling techniques due to the availability of model estimation software, relatively simple and efficient implementation algorithms, and convenient model properties such as an easy replication of the observed aggregate shares of all alternatives.  However, with the advent of Activity-Based Models (ABMs) and the corresponding extension of the modeled choice dimension to such complex choices as daily activity pattern, intra-household interactions, tour time-of-day choice, etc., certain limitations of traditional logit models have manifested themselves.  In this regard, Machine Learning (ML) methods offer a wide range of additional options for discrete choice modeling, some of them specifically designed to address these limitations.  In particular, ML methods such as Random Forest inherently consider the non-linear effects of variables conditional upon the ranges of other variables.  ML methods also offer alternative ways of handling intra-household interactions that obviate an explicit enumeration of all possible joint choices.  This paper compares performance of a traditional logit model with ML methods for a known choice model of individual Daily Activity Pattern (DAP) type.
### Motivation and statement of innovations 
Travel models and associated discrete choice structures until today have been dominated by logit modeling techniques due to the availability of model estimation software, efficient implementation algorithms, and convenient model properties such as an easy replication of the observed aggregate shares of all alternatives. However, with the advent of Activity-Based Models (ABMs) and the corresponding extension of the modeled choice dimension to complex choices, certain limitations of traditional logit models have manifested themselves:
* Logit models suffer from a combinatorial explosion of the choice set for large households when intra-household interactions are considered due to a complete enumeration of all possible joint choices.  
	* Logit model suffer from mostly linear additive specifications of utilities while identification of combined and/or non-linear effects requires multiple trials entirely specified by the modeler.  It is known in travel behavior research that variables as age, gender, income, car ownership, and household composition (for example, presence of young children) may affect travel choices in a highly nonlinear way, and the impact of each variable may change substantially depending on the other variables.
	* A logit model specification is largely separated from the model estimation.  The only formal feedback that the model estimation process provides is significance of the coefficient estimates for the given model specification.  
In this regard, ML methods offer a wide range of additional options for discrete choice modeling, some of them specifically designed to address these limitations.  This paper compares performance of a traditional logit model with ML methods for a known choice model of individual Daily Activity Pattern (DAP) type.  The focus of this comparisons is three-fold:
	* Individual prediction of daily pattern type for each person in the dataset.
	* Aggregate prediction of shares of different daily patterns.
	* New behavioral insights in terms of combinations of explanatory variables that ML could provide.
### Specifics of Coordinate Daily Activity Travel Pattern 
In most ABMs, individual DAP types are classified into three main categories: 1=Mandatory travel pattern, 2=Non-mandatory travel pattern, and 3=At-home pattern [1,3,4,5].  A mandatory travel pattern is a person who makes at least one work, university, or school trip during the day. A non-mandatory travel pattern involves at least one non-mandatory trip, with no mandatory trips taken. At-home pattern describes a person who does not participate in out-of-home activities and makes no trips.
It is important to model DAP types for all household members simultaneously due to strong intra-household interactions [5,1]. Tis leads to a simultaneous choice model for Coordinated modeling of DAP type (CDAP) [1] where a trinary choice model is applied to all household members jointly. The utility function of this joint model incorporated strong joint participation “bonuses” when different household members choose the same pattern type. However, a principal drawback of this approach is an inevitable “explosion” in number of alternatives of the joint choice with either growing household size or adding details to the DAP type itself.
In the current research, we compare the logit model described above with several ML algorithms. There are two important aspects of CDAP that are considered in these comparisons: 
	* Precision of DAP predicted for each household member at the individual and aggregate levels and identification of the key explanatory variables; in this regard the research objective was to improve the model fit and better identify variable combinations and non-linear/discrete effects.
	* Intra-household interactions; in this regard the research objective was to find a method to resolve the combinatorial explosion of the set of alternatives pertinent to standard CDAP formulations. 
### Methodology
ML in Transportation Planning  
Most model applications for transportation planning require an unbiased aggregate share fit while most ML methods do not emphasize aggregate share accuracy instead focusing on individual accuracy. However, analysis related to specific travel markets and individual behavior has put more emphasis on the accuracy of individual fit.  ML provides options to implement a framework which prioritizes individual accuracy and is subsequently adjusted to closer match aggregate shares. 
Algorithm  
The algorithm involves three sequential parts – clean and process the data, train the initial trinary classification model, and update probabilities using intra-household activity pattern correlations [3]. Two different methods to update the probabilities due to intra-household interactions are explored.   


Clean and Process Data  
The dataset used to train and evaluate the model is the regional household travel survey for Columbus, Ohio.  It contains approximately 13,400 records with individual’s socio-demographic characteristics and trip patterns for a 24-hour period.  A rich set of variables was used in both logit model and ML. The data was split into a training and test set using a stratified 2/3 to 1/3 proportion, respectively. Due to the imbalance of activity pattern labels in the dataset the synthetic minority oversampling technique was used to create a training set with a balanced set of labels. 
Train Initial Trinary Classification Model  
Six ML models were evaluated – decision tree, random forest, multi-layer perceptron, gradient boosting, support vector machine, and multinomial logistic regression. 
Using the training set each model’s parameters were validated using a 3-fold cross validation. To balance the optimization of both precision and recall while validating model parameters a weighted F1 score - the harmonic mean of precision and recall – was used. After the model parameters were validated each of the six models were evaluated by comparing F1 scores on the test set with the random forest classification model performing best with a score of .7636.  Probabilities and predictions were then calculated for every individual using the random forest classifier. 
Update Probabilities using Intra-Household Activity Pattern Correlations  
Both these approaches leverage the intra-household interactions of activity patterns to find an equilibrium between individual fit and aggregate fit. Method 1 utilizes the probability of activity patterns together in the same household. Method 2 utilizes the count of other person type-activity patterns in the household. 
Method 1:  
The probabilities from the initial random forest classifier are updated based on the joint probability of sequential activity patterns of members in a household. The joint probabilities of activity patterns were calculated by creating correlation matrices of pair-wise counts of activity patterns segmented by person type. The matrices are then normalized to sum to 1 and the subsequent values in the matrices are treated as the joint probability of each person type-activity patterns. However, to prevent the algorithm from over-predicting the dominant activity pattern when segmented by person type, the joint probability of the dominant activity pattern was set to 0.001. The probabilities of each label are then updated by applying pair-wise probability adjustments sequentially through each household. The pair-wise probability for each label is calculated as 
P(a)P(b) 〖P(a,b)〗^                                                                                                       (equation 1)
P(a) represents the maximized predicted label for the current individual and P(b) is the maximized predicted label for the next member in the household. 〖P(a,b)〗^ represents the joint probability of predicted activity patterns segmented by person type discounted by  [3].  is an optimized parameter ranging 0 to 1. This adjustment procedure is run iteratively until the algorithm reaches convergence.  

Method 2: 
To update the probabilities of the initial random forest classifier, the features representing the count of different person type-activity patterns for the other household members are re-calculated based upon the initial model choices. Then the model is applied again with the updated counts and the person type-activity pattern counts are again re-calculated. This adjustment procedure is run iteratively until the algorithm reaches convergence.
Results
Figure 1 compares the individual prediction accuracy of the initial random forest model to the logit model. The F1 scores of the logit model and random forest model were found to be .6127 and .7636, respectively. As is evident, the random forest model out performs the logit model for each of the three label classes and thus provides a 15% improvement in F1 score over the logit model.  
                          Random Forest Model                                                                          Logit Model
            
Fig. 1: Confusion Matrices of Individual Fit Measures
Figure 2 compares the aggregate share fit performance for ML method 1 and 2 as well as the logit model. For ML method 1 and 2 the shares for each adjustment is visualized to see the performance of the algorithm over each iteration. For brevity, only the full-time worker person type is visualized for comparison.  The logit model was calibrated to replicate the observed aggregate shares exactly; thus, it outperforms both method 1 and method 2. Method 1 does provide an adjustment which improves the fit by decreasing the initial random forest models over predictions and increasing the initial models under predictions. However, method 2 accentuates both the over and under predictions.  






                                        ML Method 1                                                                                        ML Method 2 
   
 Fig. 2: Comparison of Daily Activity Patterns Distributions
Figure 3 illustrates a sample decision tree showing the interaction between the combination of variables such as distance to work/school and age. One of the benefits of decision trees is the ability to model combinations of features differently. For example, in figure 3 only if an individual’s distance to work/school is less than 0.27 miles is age used in the model.  The presentation will include multiple cases where interesting and unconventional combinations of variables were found.   
 
Fig. 3: Sample Decision Tree
Conclusions and further research 
The following main conclusion can be made:
	ML methods represent a viable alternative to traditional logit models for complex multi-dimensional choices.  They may improve the individual model fit significantly.
	Different from logit models with a full set of alternative-specific constants which make the model very easy to calibrate and match aggregate targets, ML may systematically over-predict or under-predict certain choices; in this regard, making ML models easy to calibrate in aggregate sense is an important direction.
	ML methods indeed provide some additional insights into travel behavior by revealing certain non-linear combinations of variables that otherwise are difficult to guess and test with traditional logit models.     
References
	Bhat, C.R.,  K.G. Goulias, R.M. Pendyala, R. Paleti, R. Sidharthan, L. Schmitt, and H-H. Hu (2013). A Household-Level Activity Pattern Generation Model with an Application for Southern California. Transportation, Vol. 40, No. 5, pp. 1063-1086
	Bradley, M., Vovsha P. (2005) A Model for Joint Choice of Daily Activity Pattern Types of Household Members, Transportation, 32(5), 545-571.
	Garg, A., Noyola, J., Verma, R., Saxena, A., Jami, A. (2014) Correlation Based Multi-Label Classification.  Final Project – CS 221. Stanford University. 
	Lemp, J. (2014). Understanding Joint Daily Activity Pattern Choices across Household Members Using a Latent Class Model Framework. 93rd Annual TRB meeting. 
	Vuk, G., Bowman, J., Daly, A.J. & Hess, S. (2016), Impact of family in-home quality time on person travel demand, Transportation, 43(4), pp. 705–724
