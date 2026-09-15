# Data-Mining-Defect-Prediction
Course project for Data Mining predicting software defects

Software Defect Prediction: A Data Mining Approach to Software Quality Assurance
1. Problem Proposal and Description
The primary objective of this project is to predict software modules that are highly prone to defects by applying data mining techniques to historical static code metrics. Identifying buggy code prior to the formal testing phase optimizes quality assurance resources and improves overall system reliability. By treating code modules as individual data points and historical defect records as binary labels, software quality control is transformed into a highly measurable binary classification problem. This proactive defect mitigation aligns with established software engineering methodologies by shifting defect detection earlier in the development lifecycle.

2. Dataset Selection
The project utilizes the JM1 Software Defect Prediction Dataset, originally sourced from the NASA Metrics Data Program (MDP) and accessed via the OpenML repository (Dataset ID: 311) and Kaggle.
Features: The dataset comprises 21 continuous numeric features representing static code metrics, including McCabe's Cyclomatic Complexity (v(g)), Design Complexity (iv(g)), Lines of Code (loc), and Halstead base measures (length, volume, difficulty).
Target Variable: A binary categorical label (defects) indicating whether the module contains at least one defect.
Size: The dataset contains over 10,000 instances, providing a robust sample size for training classification models.







The JM1 dataset relies on two foundational theories in software engineering to quantify code complexity: McCabe's Cyclomatic Complexity and Halstead's Software Science.
McCabe's Metrics
McCabe metrics measure the structural complexity of a program by mapping its execution paths using a Control Flow Graph (CFG).
Measurement: It is calculated using the formula V(G) = E - N + 2P, where E represents the number of edges (transfers of control), N represents the number of nodes (sequential groups of statements), and P represents the connected components. A higher McCabe score indicates more branching logic (like if statements and loops), making the code harder to test.
Halstead's Metrics
Halstead metrics evaluate computational complexity by analyzing the code's vocabulary. It breaks source code down into Operators (keywords, arithmetic operators, logical operators) and Operands (variables, constants, strings).
Measurement: It relies on four base counts:
n1: Number of unique operators
n2: Number of unique operands
N1: Total occurrences of operators
N2: Total occurrences of operands
The 21 Dataset Attributes
Here is the exact meaning of every feature evaluated by your data mining model:
Line Count Metrics
loc: Total Lines of Code.
lOCode: Lines of executable code (excluding comments and blanks).
lOComment: Lines dedicated entirely to comments.
lOBlank: Blank lines used for formatting.
locCodeAndComment: Lines containing both executable code and a comment on the same line.
branchCount: The total number of branches in the control flow graph.



McCabe Structural Metrics
v(g) (Cyclomatic Complexity): The number of linearly independent paths through the code.
ev(g) (Essential Complexity): Measures the degree of unstructured code (like goto statements or deep nesting) by reducing the CFG down to its simplest structured form.
iv(g) (Design Complexity): Measures how tightly the module is coupled with other modules (how often it calls external functions).
Halstead Base Metrics
uniq_Op: Distinct operators (n1).
uniq_Opnd: Distinct operands (n2).
total_Op: Total operator count (N1).
total_Opnd: Total operand count (N2).
Halstead Derived Metrics
n (Program Length): The total number of operators and operands (N1 + N2).
v (Volume): The size of the implementation, measured in bits. Calculated as                  V = N * log_2(n1 + n2).
l (Program Level): An inverse measure of complexity. A higher level means the code is highly abstract and less complex.
d (Difficulty): How difficult the code is to write or understand. Calculated as                     D = (n1/2) * N2/n2.
i (Intelligence): The complexity of the algorithm independent of the language it is written in.
e (Effort): The mental effort required to develop or comprehend the code (E = D * V).
b (Bugs): An estimate of the number of delivered bugs expected in the module.
t (Time): Estimated time in seconds to write the module (T = E/18).








3. Preprocessing Methodology
To ensure model reliability and predictive accuracy, the following preprocessing techniques were systematically applied to the dataset:
Data Cleaning: The OpenML dataset contained missing values represented as string characters (e.g., '?'). These were identified, converted to standard NaN values, and the affected rows were dropped to maintain data integrity. The target labels were mapped from string representations ('true'/'false') to binary integers (1/0).
Feature Scaling: Software complexity metrics vary significantly in scale (e.g., Halstead Volume can be in the thousands, while Cyclomatic Complexity is typically much lower). A StandardScaler was applied to normalize the feature space to a mean of 0 and a variance of 1, preventing high-magnitude features from dominating the learning algorithm.
Class Balancing (SMOTE): Software defects are naturally occurring minority classes. Training on this imbalanced data leads to biased models. The Synthetic Minority Over-sampling Technique (SMOTE) was implemented to synthetically generate defective module samples, perfectly balancing the classes in the training set.

4. Algorithm Selection and Rationale
A Random Forest Classifier (an ensemble learning method) was selected as the primary data mining technique.
Rationale: Software structural metrics often exhibit non-linear thresholds (e.g., the likelihood of a defect increases exponentially, rather than linearly, as Cyclomatic Complexity rises). Random Forest effectively captures these non-linear decision boundaries. Furthermore, it inherently resists overfitting on highly dimensional data and provides built-in feature importance scores, which are crucial for deriving actionable software engineering insights.






5. Implementation Tools
The project was executed in a Google Colab environment using Python 3. The following libraries formed the technical stack:
pandas and numpy: For data ingestion, manipulation, and numerical operations.
scikit-learn: For dataset fetching, scaling, model training, and generating evaluation metrics.
imbalanced-learn: For applying the SMOTE oversampling technique.
matplotlib and seaborn: For visualizing the confusion matrix and feature importance rankings.

6. Results and Evaluation
The trained Random Forest model was evaluated against a 20% holdout test set (3,511 total modules). The results demonstrate a highly effective classification system:
Class
Precision
Recall
F1-Score
Support
Clean (0)
0.90
0.89
0.90
1759
Defective (1)
0.89
0.90
0.90
1752
Accuracy / Macro Avg
0.90
0.90
0.90
3511


Confusion Matrix Analysis:
True Positives (1577): Defective modules correctly identified.
True Negatives (1567): Clean modules correctly identified.
False Negatives (175): Defective modules missed by the model.
False Positives (192): Clean modules unnecessarily flagged for review.




7. Discussion, Conclusions, and Future Work
Discussion of Findings
The model achieved a 90% overall accuracy, but more importantly, a 90% Recall on the defective class. In the context of software quality assurance, high recall is the paramount metric; the cost of a false negative (releasing a bug into production) vastly outweighs the cost of a false positive (spending developer time reviewing clean code). The confusion matrix confirms that out of thousands of modules, only 175 defects bypassed the predictive filter.
The feature importance analysis yielded actionable architectural insights. loc (Lines of Code) and iv(g) (Design Complexity) were mathematically proven to be the strongest predictors of software defects. Metrics like locCodeAndComment provided almost zero predictive value.
Conclusions
By framing quality control as a data mining classification problem, this project successfully built an automated, data-driven gatekeeper. The Random Forest model, supported by rigorous preprocessing, proved highly capable of analyzing static code metrics to flag high-risk structural patterns before formal logic coverage or input space partitioning tests begin.
Future Work and Extensions
Cross-Project Defect Prediction (CPDP): Testing the generalizability of the model by training it on the NASA dataset and deploying it against modern, open-source frameworks (such as the OpenCart codebase) to evaluate cross-domain accuracy.
Integration with Agentic AI: Connecting the defect prediction model to multi-agent AI frameworks (such as RepairAgent or AFlow). Once this predictive model flags a module as highly prone to defects, an agentic reasoning architecture could be automatically triggered to traverse the execution paths and generate automated program repairs.

