# Language Models Prompting Elicits Reasoning in Large Language Models

Created: August 4, 2022 10:59 PM
Last Edited Time: August 8, 2022 7:28 AM
Level: Easy
Status: Completed 🏁
Type: NLP

- In this paper we explore a prompting method for improving the reasoning abilities of the language models. This prompting method is known as Chain of Thought, this method simply decompose the multi step problems into intermediate steps.
- With this method language models with sufficient scale can solve complex reasoning problems that are not solvable with simple methods (GPT-3)
- In chain of thought prompting, model is prompted to produce intermediate reasoning steps which will eventually solve the multi step problem.
- Chain of thought is language based model, so that it is applicable to any task that a person can solve via language.
- Chain of thought prompting also allows interpretable window through which we can debug if anything goes wrong as we have access to the intermediate steps.
- **Prompt Engineering** - In prompt engineering the description of the input is embedded as the question instead of implicitly given.
    
    ### Arithmetic Reasoning
    
    - This is the class of model where language models struggles.
    - Two benchmarks of the arithmetic reasoning are MultiArith and GSM8K, which test the ability of the language model to solve the multi step math problems.
    - With LaMDA and PaLM collection of language models, using standard prompting we were getting flat curve even if we were increasing the scale of the model, but if we will use Chain of Thought model then we will get improved performance which simply outperforms the standard model.
    - With PaLM + Chain of thought model on GSM8K dataset we will get an state of art model 58% of accuracy which was greater than last state of art model of 55% of Finetuned GPT3 + verifier.
    - With PaLM + Chain of Thought  + Self Consistency we can get upto 75% of model accuracy.
    - Chain of thought produces the mathematical equation that needs to be evaluated.
    - Equation only prompting will not help us that much until and unless it is one or two step question asked.
    - Variable computation is not the reason behind the success of the Chan of thought model, the reason is checked with the help of problem in which model has to output sequence of dots only and it takes same time as the baseline.
    - Another potential benefit of the chain of thought model is that it gives the access to the relevant knowledge  acquired during the pretraining. If we will try another configuration with prompts only given after the output then it will also perform the same as the baseline, which suggests sequential reasoning embodied in the chain of thought is useful for reasons beyonds just activating the knowledge.
    
    ### Commonsense Reasoning
    
    - Chain of thought prompting is also applicable to the commonsense reasoning.
    - Benchmarks involved in evaluation were CommonsenseQA and StrategyQA and two domain-specific tasks from BIG-BENCH collaboration regarding date understanding and sports understanding.
    - Chain of thought model has great performance on the sports understanding, for which PaLM 540B prametrized model surpassed the unaided sports enthusiast (95% vs 84%).
    - Though note the gain was very minimal in CSQA.
    - For this paper, they had sample examples ≤ 60 tokens to fit into our input context window, and also limit the examples to ≤ 2 steps to solve for a fair comparison with the eight exemplars that we composed.
    
    ### Symbolic Reasoning
    
    - This paper shows that chain of thought prompting not only enables language models to do symbolic reasoning tasks which are very hard for standard prompting, but also facilitates length generalization to inference time inputs longer than those seen in few shot exemplars.
    - Chain of thought prompting facilitates out of domain generalization, here OOD means that examples that have more steps than those in the exemplars.
    - For OOD, standard prompting fails but chain of thought prompting gives upward progression but still performed less than in domain test (for this test it has same number of steps than exemplars).
    
    ### Conclusion
    
    - Chain of thought prompting is the emergent property of model scale.
    - No language models were fine tuned for this paper.
    - Currently the usage of chain of thought model only at large scale model is very costly which makes it hard for the usage in daily life.
    - Chain of thought prompting is a simple and broadly applicable method for enhancing reasoning in language models.