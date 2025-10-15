# Benchmarking Redis Semantic Routing

This project aims to evaluate Redis for text classification, using the same benchmarks and metrics used by [Cunha et al](https://arxiv.org/pdf/2504.01930).

In order to process bulk classifications in order of thousands to hundreds of thousands, minor modifications are made to RedisVL classes.

These include:
1. Passing lower dimensions and higher batch-sizes to the OpenAIVectorizer 
2. Adding batch routing to the SemanticRouter
3. Batch embedding for threshold optimizer

# Results
| Category | Dataset     | **Traditional Approaches** |        |        | **Small Language Models (SLMs)** |        |        |        | **Large Language Models (LLMs)** |        |         |          |          |
|-----------|-------------|----------------------------|--------|--------|----------------------------------|--------|--------|--------|----------------------------------|--------|---------|----------|----------|\
|           |             | LSVM  | LR    | RF     | RoBERTa | BERT   | BART   | XLNet  | BloomZ   | LLaMa 2  | LLaMa 3.1 | Mistral  | DeepSeek |
| Topic     | DBLP        | 118.7 | 137.5 | 1017.0 | 2354.9  | 1988.0 | 2693.7 | 3874.7 | 17960.7  | 19054.5   | 16893.5    | 17002.4   | 16993.9   |
|           | Books       | 86.6  | 85.2  | 1174.0 | 2075.1  | 1790.7 | 1440.7 | 3150.0 | 15847.7  | 16765.2   | 14832.2    | 14822.0   | 14883.9   |
|           | ACM         | 81.0  | 194.9 | 713.5  | 1430.5  | 1243.5 | 1683.7 | 2385.9 | 11712.3  | 12396.4   | 10964.2    | 10992.6   | 11013.3   |
|           | 20NG        | 164.2 | 159.1 | 1160.9 | 1305.9  | 1858.7 | 1597.6 | 2034.9 | 8886.7   | 9400.5    | 8349.6     | 8372.5    | 8380.4    |
|           | OHSUMED     | 134.4 | 126.0 | 743.9  | 1306.0  | 1142.5 | 1427.9 | 2040.9 | 8644.9   | 9160.9    | 8111.9     | 8123.5    | 8144.0    |
| Sentiment | SST1        | 14.4  | 10.2  | 105.4  | 386.7   | 581.4  | 438.5  | 1585.4 | 5568.6   | 5886.3    | 5217.3     | 5230.9    | 5246.9    |
|           | pang_movie  | 15.1  | 3.5   | 76.4   | 320.5   | 512.7  | 394.0  | 560.3  | 5011.8   | 5298.7    | 4693.5     | 4706.5    | 4717.9    |
|           | Movie Review| 10.1  | 1.7   | 59.7   | 316.5   | 520.9  | 399.9  | 1352.2 | 5007.9   | 5297.2    | 4694.6     | 4706.8    | 4718.0    |
|           | vader_movie | 12.4  | 3.4   | 73.9   | 319.0   | 510.3  | 376.0  | 555.7  | 4966.3   | 5250.7    | 4656.7     | 4665.3    | 4677.9    |
|           | MPQA        | 0.6   | 2.2   | 36.3   | 318.0   | 509.3  | 401.5  | 1072.5 | 4985.7   | 5268.6    | 4670.0     | 4673.1    | 4683.7    |
| Large     | AGNews      | 162.7 | 406.7 | 3626.3 | 8464.0  | 5468.1 | 7347.0 | 10243.6| 57728.4  | 57302.2   | 51153.5    | 50981.5   | 51184.6   |
|           | Yelp_2013   | 6666.6| 8319.5| 18945.7| 18023.5 | 14967.5| 22872.9| 31684.8| 152396.2 | 152396.2  | 135039.3   | 134468.5  | 130036.1  |
|           | MEDLINE     | 2610.2| 10015.9| 26163.8| 57354.2 | 49114.3| 90709.8| 99641.6| 368407.6 | 389803.8  | 345407.9   | 344450.1  | 345134.4  |
