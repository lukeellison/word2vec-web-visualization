## Webapp for visualizing word2vec/GloVe-style word embeddings

### Compatability
This repo is forked from https://github.com/danielvarga/word2vec-web-visualization but updated to be compatable with Python 3.
> Checked with python verison 3.7.12

### Installation

```
sudo pip install numpy scipy scikit-learn annoy
```

### Starting the webservice

```
Arguments:
  -h, --help          show this help message and exit
  --glove GLOVE       filename of word embedding data file or serialized
                      GloveService. If the filename ends with .txt, it's
                      interpreted as word embedding data file, otherwise two
                      files are looked for, filename.ann and filename.json.
                      This argument is mandatory.
  --port PORT         port of service.
  --globalProjection  use a single SVD for the whole dataset instead of always
                      building it from local data. Can be overridden with
                      /glove/?q=query&globalProjection=0
```

Example:
```
python server.py --glove glove.840B.50k.300d.txt --port 8080
```

See below for details about where to get such data files from.
After starting the server, please wait until the ```Service has started.``` logline appears.


### Using the service

Click the below link, and click on some blue circles:
- http://localhost:8080/vis/

You can start from any given word:
- http://localhost:8080/vis/?q=token

This is how it looks like, although without the fancy D3.js tweening:

![The neighborhood of the word 'power'](http://www.renyi.hu/~daniel/images/glove-power.png)


There's an `lp=1` argument which overrides the server-side global SVD projection feature.
Instead of working with a global 2D embedding created at startup (see `--globalProjection`),
we create a 2D embedding just for the neighboring words:
- http://localhost:8080/vis/?q=token&lp=1&

Behind the scenes, the Javascript (D3.js) frontend communicates with a service that takes words, and
provides 2D coordinates for the words closest to the input word. This service can be directly inspected:

```
http://delight.prez.is:8080/glove/?q=cica&limit=5

->

{
    "objects": [
        [
            "cica", 
            3.5561084652545678, 
            1.7004425480123986
        ], 
        [
            "macska", 
            4.0474276933206355, 
            -0.17621523127759312
        ], 
        [
            "kutyus", 
            3.7342534819952573, 
            2.7542156028602602
        ], 
        [
            "eb", 
            4.5188699976906124, 
            -1.0474205146305975
        ], 
        [
            "kutya", 
            5.9119959213781996, 
            -1.8412600319736365
        ]
    ]
}
```


### Word embedding data

The service needs word vectors to work. The input file format is simply
whitespace-delimited, each line starting with a word and continuing
with a real word embedding vector.

For convenience, I've assembled a few datasets, you can grab them from here:

```
# English dataset, 10k most frequent words, dimension 300.
wget ftp://ftp.mokk.bme.hu/User/daniel/glove/glove.840B.10k.300d.txt

# The same dataset with the 50k most frequent words.
wget ftp://ftp.mokk.bme.hu/User/daniel/glove/glove.840B.50k.300d.txt

# Same dataset, 250k words. This takes a significant amount of time to index.
wget ftp://ftp.mokk.bme.hu/User/daniel/glove/glove.840B.250k.300d.txt

# Hungarian dataset, 200k words, dimension 200.
wget ftp://ftp.mokk.bme.hu/User/daniel/glove/glove-hu.200k.200d.txt
```

The English datasets were directly based on the [Glove](http://nlp.stanford.edu/projects/glove/) datasets, like this:

```wget http://www-nlp.stanford.edu/data/glove.42B.300d.txt.gz```

The Hungarian dataset was created by Kata Gabor and David Takacs. Thanks a lot!
The filename is my misnomer, the dataset was created using word2vec.
