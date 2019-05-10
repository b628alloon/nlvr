import json
import sys

# Load the predictions file. Assume it is a CSV.	predictionファイルを読み込みます。CSVがいいと思います。
predictions = { }
for line in open(sys.argv[1]).readlines():
  if line:
    splits = line.strip().split(",")
    # We assume identifiers are in the format "split-####-#-#.png".
    identifier = "-".join(splits[0].split(".")[0].split("-")[1:])
    prediction = splits[1]
    predictions[identifier] = prediction

# Load the labeled examples.	ラベル付された例をロードします。
labeled_examples = [json.loads(line) for line in open(sys.argv[2]).readlines() if line]

# There should be 6 x len(labeled_examples) predictions.	6 * (labeled_examplesの数) のpredictionがあるはずです。

# Get the precision by iterating through the examples and checking the value	例を通じて初期化し値をチェックすることでprecisionをゲットします。
# that was predicted.	それがpredictedです。
# Also update the "consistency" dictionary that keeps track of whether all	
# predictions for a given sentence were correct.
num_correct = 0.
consistency_dict = { }

for example in labeled_examples:
  if not example["identifier"].split("-")[0] in consistency_dict:
    consistency_dict[example["identifier"].split("-")[0]] = True
  for i in range(6):
    lookup = example["identifier"] + "-" + str(i)
    prediction = predictions[lookup]
    if prediction.lower() == example["label"].lower():
      num_correct += 1.
    else:
      consistency_dict[example["identifier"].split("-")[0]] = False

# Calculate consistency.
num_consistent = 0.
unique_sentence = len(consistency_dict)
for identifier, consistent in consistency_dict.items():
  if consistent:
    num_consistent += 1
  
# Report values.
print("precision=" + str(num_correct / total_num))
print("consistency=" + str(num_consistent / unique_sentence))

  

