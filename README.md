# Resum-Parsing-Using-NLP

## Description-
Gone are the days when recruiters used to manually screen resumes for a long time. Sifting through thousands of candidates' resumes for a job is no more a challenging task- all thanks to resume parsers. Resume parsers use machine learning technology to help recruiters search thousands of resumes in an intelligent manner so they can screen the right candidate for a job interview.

In this data science project, I have build an NLP algorithm that parses a resume and looks for the words (skills) mentioned in the job description. I have used the Phrase Matcher feature of the NLP library Spacy that does "word/phrase" matching for the resume documents. The resume parser then counts the occurrence of words (skills) under various categories for each resume that helps recruiters screen ideal candidates for a job.

## Import Packages-
```
import spacy
from spacy.tokens import DocBin
from tqdm import tqdm
import json
from sklearn.model_selection import train_test_split
```

## Dataset-
I used pre-labeled data from GitHub. Although we may use data labelling tools such as Label Studio, this will take a long time. \
```
!git clone https://github.com/laxmimerit/CV-Parsing-using-Spacy-3.git
```
## Load data-
```
cv_data = json.load(open('/content/CV-Parsing-using-Spacy-3/data/training/train_data.json', 'r'))
```
## Pre-Build Config file-
https://spacy.io/usage/training#training-data
## init fill-config
After you’ve saved the starter config to a file base_config.cfg, you can use the init fill-config command to fill in the remaining defaults. Training configs should always be complete and without hidden defaults, to keep your experiments reproducible.
```
python -m spacy init fill-config base_config.cfg config.cfg
```
## Spacy Document
```
def get_spacy_doc(file,data):
  nlp = spacy.blank('en')
  db = DocBin()

  for text, annot in tqdm(data):
    doc = nlp.make_doc(text)
    annot = annot['entities']

    ents = []
    entitiy_indices = []

    for start, end, label in annot:
      skip_entity = False
      for idx in range(start, end):
        if idx in entitiy_indices:
          skip_entity = True
          break
      if skip_entity ==True:
        continue

      entitiy_indices = entitiy_indices + list(range(start, end))

      try:
        span = doc.char_span(start, end, label=label, alignment_mode='strict')
      except:
        continue

      if span is None:
        err_data = str([start, end]) +"  "+ str(text)+ "\n"
        file.write(err_data)
      else:
        ents.append(span)
  try:
    doc.ents = ents
    db.add(doc)
  except:
    pass
  return db
```
## Split data-
```
train, test = train_test_split(cv_data, test_size = 0.3)
```
## Error data storage file
```
file = open('error.txt','w', encoding='utf-8')
db = get_spacy_doc(file, train)
db.to_disk('train_data.spacy')
db = get_spacy_doc(file, test)
db.to_disk('test_data.spacy')

file.close()
```
## init config
Instead of exporting your starter config from the quickstart widget and auto-filling it, you can also use the init config command and specify your requirement and settings as CLI arguments. You can now add your data and run train with your config. See the convert command for details on how to convert your data to spaCy’s binary .spacy format. You can either include the data paths in the [paths] section of your config, or pass them in via the command line.
```
python -m spacy train config.cfg --output ./output --paths.train ./train.spacy --paths.dev ./dev.spacy
```
