
# Social Media Sentiment Analysis

This project aims to analyze social media data, specifically tweets, to understand public sentiment towards specific topics, products, or events. Using natural language processing (NLP) techniques, we preprocess text data, extract sentiment scores, and visualize sentiment trends over time.
Dataset

The dataset used for this project is Sentiment140, which contains 1,600,000 tweets labeled as positive or negative.
Installation

To get started with this project, you need to install the required dependencies. The project is implemented using Python and requires the following libraries:

    numpy
    pandas
    nltk
    scikit-learn

You can install these dependencies using pip:

    pip install numpy pandas nltk scikit-learn

Additionally, you need to configure the Kaggle API to download the dataset:

    ! pip install kaggle
    ! mkdir -p ~/.kaggle
    ! cp kaggle.json ~/.kaggle/
    ! chmod 600 ~/.kaggle/kaggle.json

Data Preparation

    Download Dataset:
    Use the Kaggle API to download the Sentiment140 dataset.

    python

!kaggle datasets download -d kazanova/sentiment140

Extract Dataset:
Extract the downloaded zip file.

python

from zipfile import ZipFile
dataset = '/content/sentiment140.zip'

with ZipFile(dataset, 'r') as zip:
    zip.extractall()
    print('done')

Load Data:
Load the dataset into a pandas DataFrame and name the columns appropriately.

python

twitter_data = pd.read_csv('/content/training.1600000.processed.noemoticon.csv', encoding='ISO-8859-1')
column_names = ['target', 'id', 'date', 'flag', 'user', 'text']
twitter_data = pd.read_csv('/content/training.1600000.processed.noemoticon.csv', names=column_names, encoding='ISO-8859-1')

Data Cleaning and Preprocessing:

    Replace target values (4 to 1 for positive sentiment).
    Perform text preprocessing, including removing non-alphabetic characters, converting to lowercase, removing stopwords, and stemming.

python

    from nltk.corpus import stopwords
    from nltk.stem.porter import PorterStemmer
    import re

    port_stem = PorterStemmer()

    def stemming(content):
        stemmed_content = re.sub('[^a-zA-Z]', ' ', content)
        stemmed_content = stemmed_content.lower()
        stemmed_content = stemmed_content.split()
        stemmed_content = [port_stem.stem(word) for word in stemmed_content if not word in stopwords.words('english')]
        stemmed_content = ' '.join(stemmed_content)
        return stemmed_content

    twitter_data['stemmed_column'] = twitter_data['text'].apply(stemming)

Model Training

    Split Data:
    Split the data into training and test sets.

    python

from sklearn.model_selection import train_test_split

X = twitter_data['stemmed_column'].values
Y = twitter_data['target'].values

X_train, X_test, Y_train, Y_test = train_test_split(X, Y, test_size=0.2, stratify=Y, random_state=2)

Convert Text to Numerical Data:
Use TF-IDF vectorization to convert text data to numerical data.

python

from sklearn.feature_extraction.text import TfidfVectorizer

vectorizer = TfidfVectorizer()

X_train = vectorizer.fit_transform(X_train)
X_test = vectorizer.transform(X_test)

Train Model:
Train a Logistic Regression model on the training data.

python

    from sklearn.linear_model import LogisticRegression

    model = LogisticRegression(max_iter=1000)
    model.fit(X_train, Y_train)

Model Evaluation

Evaluate the model on both training and test data.

python

from sklearn.metrics import accuracy_score

# Accuracy score on the training data
X_train_prediction = model.predict(X_train)
training_data_accuracy = accuracy_score(Y_train, X_train_prediction)
print('Accuracy score on the training data:', training_data_accuracy)

# Accuracy score on the test data
X_test_prediction = model.predict(X_test)
test_data_accuracy = accuracy_score(Y_test, X_test_prediction)
print('Accuracy score on the test data:', test_data_accuracy)

Save and Load Model

Save the trained model for future predictions and load it when needed.

python

import pickle

filename = 'trained_model.sav'
pickle.dump(model, open(filename, 'wb'))

# Load the saved model
loaded_model = pickle.load(open('trained_model.sav', 'rb'))

Future Prediction

Use the saved model for future predictions.

python

X_new = X_test[200]
prediction = loaded_model.predict(X_new)
if (prediction[0] == 0):
    print('Negative tweet')
else:
    print('Positive tweet')

Conclusion

The sentiment analysis model achieved an accuracy of approximately 77.80% on the test data, indicating its effectiveness in categorizing tweets as positive or negative. This project demonstrates the application of natural language processing and machine learning techniques to analyze social media sentiment.
License

This project is licensed under the MIT License.
Acknowledgments

    The dataset used in this project is from Kaggle.
    The project was developed using Google Colab.
