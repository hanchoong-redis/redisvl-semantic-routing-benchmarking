# Benchmarking Redis Semantic Routing

This project aims to evaluate Redis for text classification, using the same benchmarks and metrics used by [Cunha et al](https://arxiv.org/pdf/2504.01930).

Note: This Semantic Routing implementation is unoptimized - More work has to be done to improve performance. Semantic Routing seems to perform inadequately when dealing with long-form texts.



## Method

Semantic Routing involves processing texts into vectors, then applying a KNN style classification with aggregations. It relies on an embedding model and fast vector search mechanisms, which is Redis' forte.

The results obtained below are acquired using [The Semantic Router](https://github.com/redis/redis-vl-python/tree/main/redisvl) of the RedisVL project, with OpenAI's `text-embedding-3-small` embedding models, with Redis running locally on an Apple M4 Pro with 24GB RAM.

All datasets retrieved from [ATC Paper Author's HFace](https://huggingface.co/waashk/datasets)

## Considerations

Further optimizations may be applied with more advanced search algorithms - This project provides one option that allows for such experimentation.

When comparing with SLMs and LLMs, take note that those models were finetuned on the datasets before classification was applied. Semantic Routing is cheaper and faster than training and applying a fully-fledged language model. It requires only access to an embedding service.

Speed wise, it is comparable to traditional methods, while exceeding their performance.

## Modifications made to RedisVL

In order to process bulk classifications at speed, minor modifications are made to RedisVL classes.

These include:
1. Passing lower dimensions and higher batch-sizes to the OpenAIVectorizer 
2. Adding batch routing to the SemanticRouter
3. Batch embedding for threshold optimizer 

## Ideas
1. Chunk reference texts, embed chunks

# Results

## Macro-F1 Scores
| Category  | Dataset      | **Semantic Routing** | **Traditional Approaches** |           | **Small Language Models (SLMs)** |           |           | **Large Language Models (LLMs)** |           |   |   |   |
| --------- | ------------ | -------------------- | -------------------------- | --------- | -------------------------------- | --------- | --------- | -------------------------------- | --------- | - | - | - |
|           |              | Redis (OOTB)         | LSVM                       | RF        | RoBERTa                          | XLNet     | LLaMa 3.1 | Mistral                          | DeepSeek  |   |   |   |
| Topic     | DBLP         | -                    | 79.7(0.7)                  | 62.6(0.9) | 81.4(0.5)                        | 81.4(0.6) | 87.8(0.4) | 86.7(0.5)                        | 86.5(0.5) |   |   |   |
|           | Books        | -                    | 84.5(0.5)                  | 75.7(0.5) | 87.2(0.6)                        | 87.3(0.4) | 93.0(0.3) | 92.6(0.4)                        | 92.2(0.5) |   |   |   |
|           | ACM          | -                    | 67.7(1.5)                  | 60.1(1.2) | 70.3(1.4)                        | 69.9(0.9) | 77.8(0.9) | 76.3(1.4)                        | 75.2(1.3) |   |   |   |
|           | 20NG         | -                    | 89.8(0.6)                  | 81.6(0.5) | 86.8(0.7)                        | 87.4(0.8) | 90.4(0.6) | 90.3(0.7)                        | 89.2(0.7) |   |   |   |
|           | OHSUMED      | -                    | 72.9(1.5)                  | 56.7(1.2) | 77.8(1.2)                        | 77.6(1.0) | 83.1(1.1) | 83.1(0.8)                        | 82.0(0.9) |   |   |   |
|           | Reuters90    | -                    | 33.2(2.3)                  | 27.0(1.6) | 41.9(2.2)                        | 41.3(2.6) | 41.5(2.6) | 41.5(2.4)                        | 41.7(2.7) |   |   |   |
|           | WOS-11967    | -                    | 85.9(0.5)                  | 83.8(0.6) | 86.8(0.4)                        | 87.0(0.7) | 89.9(0.4) | 89.1(0.7)                        | 89.6(0.7) |   |   |   |
|           | WebKB        | -                    | 70.7(1.9)                  | 64.9(1.6) | 83.0(2.0)                        | 81.9(2.5) | 87.3(1.5) | 86.0(1.3)                        | 85.7(1.8) |   |   |   |
|           | Twitter      | -                    | 64.0(1.1)                  | 45.5(1.3) | 78.4(1.8)                        | 76.4(2.1) | 78.6(1.6) | 79.3(2.4)                        | 78.4(1.8) |   |   |   |
|           | TREC         | 55.7                 | 68.8(2.5)                  | 66.0(1.8) | 95.5(0.5)                        | 94.3(1.1) | 96.1(0.8) | 96.0(0.8)                        | 96.1(0.6) |   |   |   |
|           | WOS-5736     | 65.6                 | 91.2(0.8)                  | 91.0(0.6) | 90.5(0.9)                        | 90.2(0.9) | 91.9(0.5) | 91.9(0.6)                        | 91.4(0.9) |   |   |   |
| Sentiment | SST1         | 31.5                 | 34.8(1.1)                  | 33.6(1.1) | 53.8(1.3)                        | 51.4(1.7) | 58.7(1.0) | 58.1(0.9)                        | 57.6(0.9) |   |   |   |
|           | pang_movie   | -                    | 77.3(0.8)                  | 75.5(0.8) | 89.0(0.4)                        | 88.2(0.6) | 93.6(0.4) | 93.6(0.4)                        | 92.9(0.2) |   |   |   |
|           | Movie Review | -                    | 75.9(0.9)                  | 73.5(0.5) | 89.0(0.7)                        | 86.4(3.3) | 92.0(4.0) | 93.8(0.5)                        | 92.8(0.4) |   |   |   |
|           | vader_movie  | -                    | 78.6(0.8)                  | 76.4(0.9) | 91.3(0.5)                        | 90.5(0.4) | 95.8(0.4) | 95.9(0.3)                        | 95.1(0.5) |   |   |   |
|           | MPQA         | 85                   | 78.8(0.8)                  | 78.3(0.9) | 90.2(0.8)                        | 88.6(0.5) | 91.5(0.5) | 91.4(0.4)                        | 91.1(0.4) |   |   |   |
|           | Subj         | -                    | 89.1(0.6)                  | 87.8(0.7) | 96.9(0.4)                        | 96.1(0.5) | 98.4(0.3) | 98.4(0.3)                        | 98.1(0.4) |   |   |   |
|           | SST2         | 92.2                 | 78.9(0.6)                  | 76.9(0.4) | 93.2(0.6)                        | 92.1(0.4) | 96.5(0.4) | 96.4(0.5)                        | 96.3(0.5) |   |   |   |
|           | yelp_reviews | -                    | 94.8(0.7)                  | 87.9(0.9) | 97.9(0.4)                        | 97.3(0.4) | 99.4(0.1) | 99.1(0.3)                        | 99.1(0.2) |   |   |   |
| Large     | AGNews       | 89.7                 | 91.8(0.3)                  | 88.1(0.3) | 94.2(0.2)                        | 94.0(0.1) | 95.7(0.2) | 95.0(0.1)                        | 95.6(0.3) |   |   |   |
|           | Yelp_2013    | -                    | 56.8(0.1)                  | 51.0(0.1) | 64.4(0.6)                        | 63.0(0.5) | 69.5(0.1) | 61.3(0.3)                        | 69.1(0.3) |   |   |   |
|           | MEDLINE      | -                    | 80.4(0.4)                  | 80.0(0.6) | 81.8(0.6)                        | 60.3(0.5) | 85.6(0.3) | 84.9(0.2)                        | 85.3(0.2) |   |   |   |



## Total Application Time (Training + Test)
| Category  | Dataset      | **Semantic Routing** | **Traditional Approaches** |         | **Small Language Models (SLMs)** |         |           | **Large Language Models (LLMs)** |          |   |   |   |
| --------- | ------------ | -------------------- | -------------------------- | ------- | -------------------------------- | ------- | --------- | -------------------------------- | -------- | - | - | - |
|           |              | Redis                | LSVM                       | RF      | RoBERTa                          | XLNet   | LLaMa 3.1 | Mistral                          | DeepSeek |   |   |   |
| Topic     | DBLP         | -                    | 118.7                      | 1017.0  | 2354.9                           | 3874.7  | 16893.5   | 17002.4                          | 16993.9  |   |   |   |
|           | Books        | -                    | 86.6                       | 1174.0  | 2075.1                           | 3150.0  | 14832.2   | 14822.0                          | 14883.9  |   |   |   |
|           | ACM          | -                    | 81.0                       | 713.5   | 1430.5                           | 2385.9  | 10964.2   | 10992.6                          | 11013.3  |   |   |   |
|           | 20NG         | -                    | 164.2                      | 1160.9  | 1305.9                           | 2034.9  | 8349.6    | 8372.5                           | 8380.4   |   |   |   |
|           | OHSUMED      | -                    | 134.4                      | 743.9   | 1306.0                           | 2040.9  | 8111.9    | 8123.5                           | 8144.0   |   |   |   |
| Sentiment | SST1         | -                    | 14.4                       | 105.4   | 386.7                            | 1585.4  | 5217.3    | 5230.9                           | 5246.9   |   |   |   |
|           | pang_movie   | -                    | 15.1                       | 76.4    | 320.5                            | 560.3   | 4693.5    | 4706.5                           | 4717.9   |   |   |   |
|           | Movie Review | -                    | 10.1                       | 59.7    | 316.5                            | 1352.2  | 4694.6    | 4706.8                           | 4718.0   |   |   |   |
|           | vader_movie  | -                    | 12.4                       | 73.9    | 319.0                            | 555.7   | 4656.7    | 4665.3                           | 4677.9   |   |   |   |
|           | MPQA         | 43.76                | 0.6                        | 36.3    | 318.0                            | 1072.5  | 4670.0    | 4673.1                           | 4683.7   |   |   |   |
| Large     | AGNews       | 1154.1               | 162.7                      | 3626.3  | 8464.0                           | 10243.6 | 51153.5   | 50981.5                          | 51184.6  |   |   |   |
|           | Yelp_2013    | -                    | 6666.6                     | 18945.7 | 18023.5                          | 31684.8 | 135039.3  | 134468.5                         | 130036.1 |   |   |   |
|           | MEDLINE      | -                    | 2610.2                     | 26163.8 | 57354.2                          | 99641.6 | 345407.9  | 344450.1                         | 345134.4 |   |   |   |

