

Prompt Design (Track C)
====
System Prompt
====
The following zero-shot system prompt was used to instruct the LLM to generate structured explanations for machine learning predictions.

You are an AI assistant that explains machine learning classification predictions.

Your task is to generate a structured explanation for a movie revenue prediction.

You will receive:
1. A set of movie feature values.
2. The predicted class produced by the machine learning model.
3. The predicted probability for that class.

Return ONLY valid JSON using the following schema:

{
  "prediction_label": "string",
  "confidence_level": "low | medium | high",
  "top_reason": "string",
  "second_reason": "string",
  "next_step": "string"
}

Instructions:
=====
- Return only valid JSON without Markdown or additional text.
- Do not invent or modify feature values.
- Do not claim that a feature caused the prediction; instead, describe features that may have influenced it.
- Base the explanation only on the provided feature values and prediction.
- Determine confidence_level using the predicted probability:
    - High: probability >= 0.80
    - Medium: probability >= 0.60 and < 0.80
    - Low: probability < 0.60
- Keep each explanation concise and easy to understand.
User Prompt Template
Movie Features:{feature_values}

Predicted Class:{predicted_class}

Predicted Probability:{predicted_probability}

Generate the structured JSON explanation.where:

{feature_values} → Dictionary containing the movie features.
{predicted_class} → Prediction returned by the machine learning model.
{predicted_probability} → Probability returned by predict_proba().
Temperature Comparison
Input	Output at Temp = 0	Output at Temp = 0.7	Key Difference
Input 1	Runtime was identified as the second reason, with marketing focused on genre appeal.	Budget was identified as the second reason, with a broader marketing recommendation.	Both outputs predict High Revenue, but temperature 0.7 changes the supporting reasons and recommendation wording.
Input 2	States the genre mix can attract a wide audience and mentions production quality.	Uses more cautious wording such as "may attract" and "could have allowed", with a broader marketing recommendation.	The prediction remains the same, while the explanation becomes more varied and less deterministic.
Input 3	Focuses on the movie genres and release year as reasons for the prediction.	Uses more descriptive language about genre popularity and audience interest.	Both outputs have the same prediction, but temperature 0.7 produces richer and more varied wording.
Why Temperature = 0?

For this project, temperature = 0 was selected because the task requires structured JSON output that is consistent and reproducible. At a temperature of 0, the LLM always chooses the highest-probability next token, resulting in deterministic responses for the same input. This ensures that repeated executions produce the same JSON structure and content, making schema validation more reliable and reducing formatting inconsistencies.

In contrast, temperature = 0.7 introduces controlled randomness by sampling from a broader probability distribution. As observed in the experiments, the predicted class remained unchanged, but the wording, supporting reasons, and recommendations varied between runs. While this variability can produce more natural and expressive explanations, it is less suitable for applications that require consistent, machine-readable structured outputs. Therefore, temperature = 0 was chosen for the final implementation to ensure reliable, repeatable, and schema-compliant JSON responses.

Structured Output Handling (Track C)
===
The best-performing machine learning pipeline (best_model.pkl) was successfully loaded using joblib.load(). Three manually created movie feature vectors were passed through the pipeline to generate predictions and prediction probabilities. Each prediction was then sent to the LLM to obtain a structured JSON explanation. The returned JSON was parsed using json.loads() and validated against the predefined schema using jsonschema.validate(). All three responses successfully passed schema validation.

Structured Output Validation Results
Feature Input	Predicted Class	Probability	Explanation JSON	Validation Status
Input 1	1 (High Revenue)	0.9540	{"prediction_label":"High Revenue","confidence_level":"high","top_reason":"The movie belongs to popular genres like Action, Adventure, Fantasy, and Science Fiction.","second_reason":"The movie has a runtime of 130 minutes, which is suitable for engaging audiences.","next_step":"Consider marketing strategies to capitalize on the movie's genre appeal and runtime to attract a wide audience."}	Passed
Input 2	1 (High Revenue)	0.6753	{"prediction_label":"High Revenue","confidence_level":"medium","top_reason":"Genre combination of Comedy, Drama, and Romance.","second_reason":"Moderate budget of $10,000,000.","next_step":"Consider promoting the movie highlighting its genre mix to attract a wider audience."}	Passed
Input 3	1 (High Revenue)	0.8470	{"prediction_label":"High Revenue","confidence_level":"high","top_reason":"The movie belongs to genres like Crime, Drama, Mystery, and Thriller which are popular among audiences.","second_reason":"The movie was released in 2015, a year that could have attracted a significant audience.","next_step":"Consider promoting the movie highlighting its genre mix and the release year to attract more viewers."}	Passed
Summary

All three feature-vector inputs were successfully processed by the prediction pipeline. The LLM returned valid structured JSON explanations for every input, and each response passed schema validation without requiring the fallback mechanism. This demonstrates that the complete Track C workflow—from model prediction, prompt construction, LLM explanation generation, JSON parsing, and schema validation—operates correctly and produces consistent, machine-readable explanations.

Guardrail Test Results
===
PII Guardrail Demonstration

PII Guardrail Demonstration

A regex-based PII (Personally Identifiable Information) guardrail was implemented before every LLM API call. The guardrail checks the input for email addresses and phone numbers. If PII is detected, the request is blocked and the LLM is not called.

Guardrail Test Results
Test Input	Result
Input containing an email address (john.doe@gmail.com)	Blocked – Input blocked: PII detected.
Movie feature input (budget, runtime, genres, prediction)	Allowed – LLM returned a valid JSON explanation.





End-to-End Feature Demonstration (Track C)
====================
The complete prediction explanation pipeline was executed on three manually created movie feature vectors. For each input, the machine learning model generated a prediction, the LLM produced a structured JSON explanation, the response was validated against the predefined JSON schema, and the PII guardrail was checked before every API call.

Input	LLM Output	Valid JSON (Pass/Fail)	Pass/Block (Guardrail Result)
Input 1 (High-budget Action/Adventure movie)	{"prediction_label":"High Revenue","confidence_level":"high","top_reason":"Action, Adventure, Fantasy, Science Fiction, Thriller genres","second_reason":"Release year 2020","next_step":"Consider marketing and distribution strategies for the movie."}	Pass	Pass
Input 2 (Low-budget Comedy/Drama movie)	{"prediction_label":"High Revenue","confidence_level":"medium","top_reason":"Genre: Comedy, Drama, Romance","second_reason":"Release Year: 2018","next_step":"Further analyze audience preferences and marketing strategies."}	Pass	Pass
Input 3 (Medium-budget Crime/Thriller movie)	{"prediction_label":"High Revenue","confidence_level":"high","top_reason":"The movie may have a high revenue potential due to the genre combination of Crime, Drama, Mystery, and Thriller.","second_reason":"The movie's release year of 2015 may have contributed to its high revenue prediction.","next_step":"Further analysis on audience preferences and marketing strategies could provide additional insights."}	Pass	Pass
Summary

All three feature-vector inputs successfully passed the PII guardrail because they contained only movie-related features and no personally identifiable information. The LLM returned valid JSON responses for each input, and all responses successfully passed schema validation. This demonstrates that the complete pipeline—from model prediction, through prompt construction, LLM explanation generation, JSON parsing, and schema validation—works correctly and reliably for the selected Track C implementation.
