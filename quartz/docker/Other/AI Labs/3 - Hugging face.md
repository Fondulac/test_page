
```python
pipline() #load data from a huggingface database

#example
def hugging_face_example:
	from transformers import pipeline
	sentiment_analysis pipeline("sentiment-analysis", model="distilbert-base-        uncased-finetuned-sst-2-english") #import the dataset
	input_text = ["TEXXT SDLFKJSD:LFKJSDF"]
	result = sentiment_analysis(input_text)
	print(result)
```