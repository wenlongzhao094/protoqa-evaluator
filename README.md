API and methods for evaluating Family Feud style question/answers.

Example use:
```python
from family_feud_evaluator.data_processing import load_data_from_jsonl
from family_feud_evaluator.evaluation import evaluate, family_feud

question_data = load_data_from_jsonl('path/to/dataset_lines.jsonl')
evaluate(family_feud, question_data, answers_dict={'q0': ['umbrella', 'hat', 'sun glasses']})
# Returns {'q0': 0.3838383838}
```
As above, model answers should be specified as a dict of lists. There are other evaluation methods available, for example:
```python
from family_feud_evaluator.evaluation import fast_money
evaluate(fast_money, question_data, answers_dict={'q0': ['umbrella', 'hat', 'sun glasses']})
# Returns {'q0': 1.0}
```
It is easy to create your own evaluation method using the `general_eval`. For example, let's make a set intersection evaluation which simply tells us what percentage of the true answer clusters we got right, and let's also use `longest_common_subsequence_score`  as our answer scoring function so that 'sun glasses' gets counted in the 'sunglasses' cluster:
```python
from family_feud_evaluator.evaluation import general_eval
from family_feud_evaluator.scoring import longest_common_subsequence_score
from functools import partial

soft_lcsubsequence_set_int = partial(
    general_eval,
    answer_cluster_scoring_func=longest_common_subsequence_score,
    assign_cluster_scores = False, # This is what makes it a set, it turns off the cluster counts
)

evaluate(soft_lcsubsequence_set_int, question_data,
         answers_dict={'q0': ['umbrella', 'hat', 'sun glasses']})
# Returns: {'q0': 0.3896103896103896}
```