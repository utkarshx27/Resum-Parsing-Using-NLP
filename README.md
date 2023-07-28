# CV Parsing using Spacy

CV Parsing using Spacy is a personal project that demonstrates how to extract information from resumes (CVs) using the powerful natural language processing library, Spacy. The project focuses on creating a custom named entity recognition (NER) model to identify specific entities in the resume text, such as names, work experiences, and years of experience.

## Requirements

To run this project, you'll need the following libraries and tools:

- Python 3.x
- Spacy
- spacy-transformers
- tqdm
- json
- scikit-learn
- PyMuPDF
- NVIDIA GPU (optional but recommended for faster training)

## Installation

First, install the necessary dependencies:

```bash
pip install spacy spacy-transformers tqdm scikit-learn PyMuPDF
```

## Data

The training data for this project is stored in a JSON format with annotated entities. You can find the data in the `data/training/train_data.json` file. The data contains resume texts along with their annotated entities like names, work experiences, and more.

## Training

1. Install NVIDIA GPU drivers (if available) to enable faster training using the GPU.
2. Clone the project repository from GitHub:

```bash
git clone https://github.com/laxmimerit/CV-Parsing-using-Spacy-3.git
```

3. Initialize the Spacy configuration file:

```bash
python -m spacy init fill-config /path/to/CV-Parsing-using-Spacy-3/data/training/base_config.cfg /path/to/CV-Parsing-using-Spacy-3/data/training/config.cfg
```

4. Train the custom NER model using the training data:

```bash
python -m spacy train /path/to/CV-Parsing-using-Spacy-3/data/training/config.cfg --output ./output --paths.train ./train_data.spacy --paths.dev ./test_data.spacy --gpu-id 0
```

The trained model will be saved in the `output` directory.

## Parsing Resumes

After training the model, you can use it to parse resumes and extract relevant information:

```python
import spacy

# Load the trained model
nlp = spacy.load('/content/output/model-best')

# Sample resume text
resume_text = 'my name is Utkarsh Singh. I worked at Google. I have 10 years of experience'

# Process the resume text with the model
doc = nlp(resume_text)

# Print extracted entities
for ent in doc.ents:
    print(ent.text, "->", ent.label_)
```

## Parsing PDF Resumes

The project also includes functionality to parse resumes from PDF files:

```python
import spacy
import fitz

# Load the trained model
nlp = spacy.load('/content/output/model-best')

# Path to the PDF file containing the resume
pdf_file = '/content/CV-Parsing-using-Spacy-3/data/test/Alice Clark CV.pdf'

# Extract text from the PDF
pdf_document = fitz.open(pdf_file)
text = " "
for page in pdf_document:
    text += page.get_text()
text = text.strip()
text = ' '.join(text.split())

# Process the text with the model
doc = nlp(text)

# Print extracted entities
for ent in doc.ents:
    print(ent.text, "--------->", ent.label_)
```

## Conclusion

CV Parsing using Spacy is a valuable project that showcases the power of Spacy and demonstrates how to create custom NER models to extract specific information from resumes. This project can be extended to handle various types of documents and help in automating information extraction tasks in real-world applications. Feel free to explore the project further and customize it to suit your specific needs.

Happy parsing!
