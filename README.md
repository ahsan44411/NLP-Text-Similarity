# Text-Similarity

# Word2Vec Guide (Code in Word2Vec.ipynb)

Nearly every NLP algorithm uses a word embedding technique for representation of document vocabulary. It allows algorithms to capture the context of words in documents, semantics, and syntactic similarity and relation with other words.

Word2Vec uses a vector representation of words. For Example sentence “I go to school” will be represented as one-hot vectors in transpose i.e.
I = [1,0,0,0]
go = [0,1,0,0]
to = [0,0,1,0]
school = [0,0,0,1]

But for words with the same contextual information, we introduce some dependence. So those same words occupy close spatial positions i.e good and great, pistol and gun occupy the space close to each other. 

How to find the sentence similarity index(Linux)
Download Google Word2Vec from here

wget http://magnitude.plasticity.ai/word2vec/GoogleNews-vectors-negative300.magnitude

Install package pymagnitude
pip3 install pymagnitude

If this does not work use the following

SKIP_MAGNITUDE_WHEEL=1 pip3 install pymagnitude==0.1.46 -vvvv

Here’s the code

from pymagnitude import Magnitude
vectors = Magnitude('GoogleNews-vectors-negative300.magnitude')



Find similarity using
print(vectors.similarity("Have a good day", "Have a nice day"))


# Glove Guide (Code in GloVe.ipynb)

GloVe is a Natural Language processing algorithm that uses Word Embedding techniques to map words in vectors just like Word2Vec but GloVe is slightly different in the way it forms the context of each word with other words. GloVe uses the Global context and local context of each other with other words making a much more complex and larger word-context matrix as compare to Word2Vec which uses local context meaning that Word2Vec learn about the context for a word in a sentence whereas GloVe learns the context of words in the entire corpus.
Code
Download and unzip the pre-train GloVe files in your work directory.

wget 'http://nlp.stanford.edu/data/glove.6B.zip'
unzip glove.6B.zip


Import and download stop words


          import nltk
          nltk.download('stopwords')


Import these helper functions to load the model, process the sentences, and remove stop words function.


      def loadGloveModel(gloveFile):
          print ("Loading Glove Model")
          with open(gloveFile, encoding="utf8" ) as f:
              content = f.readlines()
          model = {}
          for line in content:
              splitLine = line.split()
              word = splitLine[0]
              embedding = np.array([float(val) for val in splitLine[1:]])
              model[word] = embedding
          print ("Done.",len(model)," words loaded!")
          return model

      def preprocess(raw_text):

          # keep only words
          letters_only_text = re.sub("[^a-zA-Z0-9]", " ", raw_text)

          # convert to lower case and split 
          words = letters_only_text.lower().split()

          # remove stopwords
          stopword_set = set(stopwords.words("english"))
          cleaned_words = list(set([w for w in words if w not in stopword_set]))

      def cosine_distance_between_two_words(word1, word2):
          import scipy
          return (1- scipy.spatial.distance.cosine(model[word1], model[word2]))


      def calculate_heat_matrix_for_two_sentences(s1,s2):
          s1 = preprocess(s1)
          s2 = preprocess(s2)
          result_list = [[cosine_distance_between_two_words(word1, word2) for word2 in s2] for word1 in s1]
          result_df = pd.DataFrame(result_list)
          result_df.columns = s2
          result_df.index = s1
          return result_df

      def cosine_distance_wordembedding_method(s1, s2):
          import scipy
          vector_1 = np.mean([model[word] for word in preprocess(s1)],axis=0)
          vector_2 = np.mean([model[word] for word in preprocess(s2)],axis=0)
          cosine = scipy.spatial.distance.cosine(vector_1, vector_2)
          print('Word Embedding method with a cosine distance asses that our two sentences are similar to',round((1-cosine)*100,2),'%')

      def heat_map_matrix_between_two_sentences(s1,s2):
          df = calculate_heat_matrix_for_two_sentences(s1,s2)
          import seaborn as sns
          import matplotlib.pyplot as plt
          fig, ax = plt.subplots(figsize=(5,5)) 
          ax_blue = sns.heatmap(df, cmap="YlGnBu")
          # ax_red = sns.heatmap(df)
          print(cosine_distance_wordembedding_method(s1, s2))
          return ax_blue

      Load one of the downloaded files. I am using glove.6B.50d.txt

      gloveFile = "glove.6B.50d.txt"
      model = loadGloveModel(gloveFile)


      Output the similarity between sentences and a heat map between words.

      ss1 = "Black Wallet  Cash 3500 Rs CNIC Drivers license Nust Student Card"
      ss2 = "Black Wallet  Cash 5000 Rs CNIC Drivers license Nust Student Card "


      heat_map_matrix_between_two_sentences(ss1,ss2)
