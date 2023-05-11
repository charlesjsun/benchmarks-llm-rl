# LM RL Benchmark

## Twenty Questions
There are three datasets: `convos_1k.json`, `convos_10k.json`, and `convos_100k.json`, containing 1K, 10K, and 100K conversations respectively.
The conversations between the three are distinct, so either one can serve as the train and eval set.

The dataset JSON has the following structure:
``` JSON
[
    {
        "lines": [
            "Is it an animal? Yes.",
            "Is it a mammal? Yes.",
            "Is it a domesticated animal? No.",
            "Is it an herbivore? No.",
            "Is it a carnivore? Yes.",
            "Is it a large cat? Yes.",
            "Is it a lion? Yes."
        ],
        "correct": true,
        "word": [
            "Lion"
        ]
    },
    ...
    ...
]
```

The JSON contains a list of conversations. Each conversation is a dictionary with three keys: `"lines"`, `"correct"`, and `"word"`.
- `"lines"` is a list of strings representing the conversation. The maximum length of the list is 20. Each line consists of a question (ending with a question mark), along with an answer by the oracle of "Yes." or "No." (there's always a period at the end of the line). You can use the provided `split_question_answer` function to split the line into question and answer (`twenty_questions/data.py`).
- `"correct"` is a boolean representing whether the guesser correctly guessed the word.
- `"word"` is the word that the guesser is guessing. `"word"` is represented as a list of strings. Typically, this list contains only one string. However, some words are often said in different ways such as "Television" vs "TV", or "Lego" vs "Lego set", and we want the oracle to correctly parse the question with these variations to determine if the guesser has guessed the correct word. See `twenty_questions/data.py` line 303, the `is_done` function to see how this is used.

There are a total of 158 unique objects/words belong to 17 different categories. Check `DEFAULT_OBJECT_DICT` in `twenty_questions/data.py` to see the whole list (note that semicolons in the string denotes word variants, e.g. "Television;TV"). There is a varying distribution of successfully guessed vs unsuccessful conversations between the different objects, but each object is guaranteed to have at least one successful conversation.

`twenty_questions/data.py` contains a lot of code for loading the dataset. Feel free to ignore them if you find them unnecessary for your pipeline. You will need to install `nltk` to use the code.
``` shell
pip install ntlk
python -m nltk.downloader averaged_perceptron_tagger
```

## Car Dealer
The dataset is split into a training and eval set `train.json` and `eval.json`. Together there are 19408 conversations. Feel free to combine them and split it into train/eval yourself.

The dataset JSON has the following structure:
``` JSON
{
    "polite": [
        {
            "buyer_info": {
                "personality": "polite",
                "preferred_brands": "a Toyota",
                "preferred_features": [
                    "backup camera",
                    "third-row seating",
                    "heated seats",
                    "Apple CarPlay"
                ],
                "budget": 90000,
                "preferred_type": "SUV"
            },
            "lines": [
                {
                    "role": "Seller",
                    "text": "Hi there, welcome to our Toyota dealership. How can I assist you today?"
                },
                {
                    "role": "Buyer",
                    "text": "Hi, I'm interested in purchasing a Toyota SUV. I'm looking for one with a backup camera, third-row seating, heated seats, and Apple CarPlay."
                },
                {
                    "role": "Seller",
                    "text": "Great! We have a few models that match your requirements. What's your budget for this purchase?"
                },
                ...
            ],
            "output": {
                "car_bought": true,
                "msrp": 80000,
                "buy_price": 83000
            },
            "original_conversation": ...
        },
        ...
        ...
    ]
    ...
    ...
]
```

The JSON contains a dictionary that maps each personality to a list of conversations with that buyer personality. Each conversation is a dictionary with four keys: `"buyer_info"`, `"lines"`, `"output"`, and `"original_conversation"`.
- `"buyer_info"` is a dictionary that contains information on the buyer.
- `"lines"` is a list of strings representing the conversation. Each line is a dictionary that indicates which `role` said this line (`Seller` or `Buyer`), and the actual `text` that was said.
- `"output"` is a dictionary indicating the final result of the conversation, whether the car was bought/sold, the MSRP of the car in question, and the price that the car was bought at (or none if car not sold).
- `"original_conversation"` is a string containing the raw output of the LLM that generated this conversation.

`car_dealer/data.py` contains a lot of code for loading the dataset. Feel free to ignore them if you find them unnecessary for your pipeline.
