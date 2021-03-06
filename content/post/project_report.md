+++
title = "Project Report Phase 1" 
date = 2018-12-21T00:00:00

# Authors. Comma separated list, e.g. `["Bob Smith", "David Jones"]`.
authors = ["Shiva Prasad Reddy Katta"]

#List format.
#0 = Simple
#1 = Detailed
#2 = Stream
list_format = 2

# Tags (optional).
#   Set `tags = []` for no tags, or use the form `tags = ["A Tag", "Another Tag"]` for one or more tags.
tags = ["Server","Installation"]

# Step to follow.


#Optional featured image (relative to static/img/ folder).
[header] 
image = "" 
caption = "" 
+++
First things first, let me be honest. I’ve absolutely ‘zero’ prior knowledge in coding and I’ve not worked with Android. As a matter of fact, I've done my Bachelors in Electronics and Communication Engineering, and I have no idea about programming languages. I took this course because it is challenging and that I can learn something new. When I started this project, it was like Greek and Latin, and I couldn’t figure out anything. In fact, I gave up many times. Taking help from others and online communities helped me a lot.<br/><br>

[Click here](https://drive.google.com/file/d/1NmPWFmBUvuRsREOQqVEc7SSpMyDYtBIf/view) to view the dataset I have used for the project.<br/><br>

The aim of this project is to build a movie recommendation engine, however, I was able to accomplish only the phase 1 of this project which is the search engine. The search engine was made possible by utilising concepts such as TF-IDF and Cosine Similarity. First I imported python libraries like numpy and pandas. The dataset I chose has a total of 24 columns, but out of all of them I chose only 3 columns which are 'overview', 'tagline', and 'genres'. I combined all these columns into a single column named as text_dump.

    def text_dump(data):
    return data['original_title'] + ' ' + data['overview'] + ' '  + data['tagline'] + ' ' .join(data['genres'])
    df['unprocesses_text_col'] = df.apply(text_dump, axis=1)
    df['unprocesses_text_col'][23455]
    
Now, I converted the text into lower case and removed punctuation, stopwords from it.

    df.unprocesses_text_col = df.unprocesses_text_col.apply(lambda x: x.lower())
    df['unprocesses_text_col'][23455]
    df['unprocesses_text_col'] = df['unprocesses_text_col'].str.replace('[^\w\s]','')
    df['unprocesses_text_col'][23455]
    cachedStopWords = stopwords.words("english")

    def removeStopWords(text):
      text = ' '.join([word for word in text.split() if word not in cachedStopWords])
      return text

    df['processed_text_col'] = df['unprocesses_text_col'].apply(removeStopWords)
    df['processed_text_col'][23455]
    
The next step involves stemming and I used Snowball Stemmer which is the version 2 of Porter stemmer.

    stemmer = SnowballStemmer('english')

    def stemmerk(text):
      words = word_tokenize(text)
      return ' '.join([stemmer.stem(w) for w in words])
    df['processed_text_col'] = df['processed_text_col'].apply(stemmerk)
    df['processed_text_col'][23455]

Now, I will generate a count matrix for each movie and calculate the TF-IDF.

    count = CountVectorizer()
    count_matrix = count.fit_transform(df['processed_text_col'])
    tfidf_transformer = TfidfTransformer()
    train_tfidf = tfidf_transformer.fit_transform(count_matrix)

The following function will take a query as input and return the top 3 results using Cosine Similarity

    def movieList(entry):
    entry = entry.lower()
    entry = re.sub(r'[^\w\s]','',entry)
    entry = removeStopWords(entry)
    entry = stemmerk(entry)
    entry_count_matrix = count.transform([entry])
    entry_tf_idf = tfidf_transformer.transform(entry_count_matrix)
    similarity_score = cosine_similarity(entry_tf_idf, train_tfidf)
    sorted_indexes = np.argsort(similarity_score).tolist()
    return df[['original_title', 'overview']].iloc[sorted_indexes[0][-3:]]
    
The libraries I used in Python are numpy, pandas, nltk, sklearn, etc.

Coming to the application part, I'm using an Android app to interact with the server and fetch the results. A general outline of the system architecture can be seen by [clicking here](https://drive.google.com/file/d/182RaD7LmovFUJ3lb4A7_eMjgSc3YDmtk/view?usp=sharing)

The python output above is converted into Json data so that it is compatible to be displayed in the Android app.

Upon opening the app, the user can find two options to login: one is to enter the credentials and the other is the guest login. After logging in, user can find 'search for movies' button. As I have done only phase 1, there are no options for classification and recommendation in the application. Now after clicking the 'search for movies' button, the next screen will display a text field and a 'Go' button. Enter the query and hit the Go button. The following screen will display three results.
Here are the screenshots of the Android app
1) [Login Screen](https://drive.google.com/file/d/1-JNQsWsgnjMv6k8GTCLP87v5VGXOv9oB/view?usp=sharing)
2) [Home Screen](https://drive.google.com/file/d/1-3O_Hgiu3DqnI3Byqjdnp0DG_NAp7X_n/view?usp=sharing)
3) a) [Search Screen](https://drive.google.com/file/d/1-3UScSRycysDAEBiFhXJmnzFBUOze2rj/view?usp=sharing)
   b) [Search Screen](https://drive.google.com/file/d/1-4iSvyCk-Iebi9Toslc2Bky1Hvln5wD2/view?usp=sharing)
4) [Result Screen](https://drive.google.com/file/d/1-55HhhQooPmfMI6Y1NgYW4BA_nGK_jno/view?usp=sharing)


# References
For converting the text into lower case I referred to Chaim Gluck code from his post on Medium: https://medium.com/@chaimgluck1/have-messy-text-data-clean-it-with-simple-lambda-functions-645918fcc2fc

For removing punctuation I referred Bob Haffner’s code from StackOverflow: https://stackoverflow.com/questions/39782418/remove-punctuations-in-pandas

For punctuation I also referred Quora post: https://www.quora.com/How-do-I-remove-punctuation-from-a-Python-string

For removing stop words I referred code provided by Andy Rimmer in StackOverflow: https://stackoverflow.com/questions/19560498/faster-way-to-remove-stop-words-in-python

For tfidf and sorting the results I referred Heet Madhus code : https://github.com/Heetmadhu/Movie-Recommendation/blob/master/MovieSearch.ipynb

For cosine similarity I referred to Emma Giraldi code: https://github.com/emmagrimaldi/Content_based_movie_recommender/blob/master/.ipynb_checkpoints/EG_project5-checkpoint.ipynb

 https://github.com/BhaskarTrivedi/QuerySearch_Recommentation_Classification

