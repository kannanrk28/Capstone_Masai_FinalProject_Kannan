Environment Setup for Evaluator
======
The Part 4 notebook has been successfully executed and validated in Google Colab using my own OpenRouter API credentials. For security reasons, the API key is not included in this repository.

To run the notebook, please choose one of the following options:

Create a .env file using the provided sample.env template and add your own OpenRouter API credentials, or
Run the notebook without a .env file. The notebook will automatically prompt you to securely enter:
LLM_API_KEY
LLM_API_URL (press Enter to use the default value)
LLM_MODEL (press Enter to use the default model)

Once the required configuration is provided, the notebook will execute successfully and reproduce the results presented in this project.

Part 4 – Outline
=======

1. LLM API Configuration
Configured the LLM API using secure environment variables.
Implemented a reusable call_llm() function to communicate with the LLM through HTTP POST requests.
Verified the API connection using a simple test prompt.

2. Prompt Engineering
Selected Track C – Model Prediction Explanation Pipeline.
Designed a zero-shot system prompt to generate structured JSON explanations.
Created a reusable user prompt template containing movie feature values, predicted class, and prediction probability.
Compared model responses using temperature = 0 and temperature = 0.7.

3. Structured Output Handling
Loaded the best-performing machine learning pipeline (best_model.pkl).
Generated predictions and prediction probabilities for three hand-crafted movie feature vectors.
Sent prediction information to the LLM.
Parsed the returned JSON responses.
Validated all responses against a predefined JSON schema.

4. Safety Guardrails
Implemented a regex-based PII (Personally Identifiable Information) guardrail.
Detected email addresses and phone numbers before every LLM request.
Blocked requests containing sensitive information.
Demonstrated both blocked and successful API requests.

5. End-to-End Demonstration
Executed the complete ML-to-LLM pipeline on three distinct movie feature vectors.
Generated structured explanations for every prediction.
Verified JSON schema validation.
Confirmed successful operation of the guardrail and explanation pipeline.

LLM API Connection Test
=========================

A reusable call_llm() function was implemented to communicate with the LLM API using an HTTP POST request. Before sending the request, the input was passed through the PII guardrail to ensure that it did not contain any personally identifiable information.

Test Result
Test	Result
Guardrail Status	Pass
LLM Response	Hello

Interpretation
=====
The input successfully passed the PII guardrail because it contained no email addresses or phone numbers. The request was then sent to the LLM API, which returned the expected response "Hello". This confirms that:

The API endpoint is correctly configured.
The API key is valid.
The call_llm() function successfully sends requests and receives responses.
The response is correctly extracted from the API output.
The guardrail allows safe inputs to proceed to the LLM.

Conclusion
=====
The successful response demonstrates that the LLM API connection is functioning correctly and is ready to be used for generating structured explanations in the subsequent tasks of the project.

PII Guardrail Demonstration
====
A regex-based Personally Identifiable Information (PII) guardrail was implemented before every LLM API call. The guardrail checks the user input for email addresses and phone numbers. If PII is detected, the request is blocked and is not sent to the LLM. Otherwise, the request proceeds normally.

Guardrail Test Results
Test Input	Guardrail Status	LLM Response	Result
Input containing an email address (john.doe@gmail.com)	Block	None	Input blocked because PII was detected.
Movie feature input (budget, runtime, prediction)	Pass	Valid JSON explanation returned by the LLM	Input contained no PII and was successfully processed.
Example Response for Safe Input
{
  "prediction_label": "High Revenue",
  "confidence_level": "high",
  "top_reason": "The high budget of $150,000,000.",
  "second_reason": "The runtime of 130 minutes.",
  "next_step": "Consider promoting the movie extensively to maximize its revenue potential."
}

Interpretation
====
The first test contained an email address, which matched the regular expression used by the PII guardrail. As a result, the request was blocked before reaching the LLM, and no response was generated. The second test contained only movie-related feature values and prediction information, so it successfully passed the guardrail. The LLM returned a valid structured JSON explanation that could be further validated against the predefined JSON schema.

Conclusion
=====
The PII guardrail successfully protected sensitive information by preventing inputs containing email addresses from being transmitted to the external LLM service. At the same time, legitimate movie prediction inputs were processed without interruption, demonstrating that the guardrail effectively balances privacy protection with normal application functionality.



Structured Output Handling (Track C)
=========
The best-performing machine learning pipeline (best_model.pkl) was loaded using Joblib and used to predict the revenue class for three manually created movie feature vectors. For each prediction, the feature values, predicted class, and predicted probability were incorporated into a structured prompt and sent to the LLM. The returned response was parsed using json.loads() and validated against the predefined JSON schema using jsonschema.validate(). All three responses successfully satisfied the schema requirements and required no fallback handling.

Structured Output Validation Results
====
Feature Input	Predicted Class	Probability	Explanation JSON	Validation Status	Guardrail Result

Input 1	1 (High Revenue)	0.9540	{"prediction_label":"High Revenue","confidence_level":"high","top_reason":"The movie belongs to popular genres like Action, Adventure, Fantasy, and Science Fiction.","second_reason":"The movie falls under the Thriller category, which often attracts a large audience.","next_step":"Consider investing in marketing campaigns to capitalize on the movie's potential success."}	Passed	Pass

Input 2	1 (High Revenue)	0.6753	{"prediction_label":"High Revenue","confidence_level":"medium","top_reason":"The movie is a Comedy, Drama, and Romance genre combination.","second_reason":"The movie was released in 2018.","next_step":"Consider promoting the movie to target the audience interested in Comedy, Drama, and Romance genres."}	Passed	Pass

Input 3	1 (High Revenue)	0.8470	{"prediction_label":"High Revenue","confidence_level":"high","top_reason":"The movie has elements of Drama, Crime, Mystery, and Thriller genres.","second_reason":"The movie was released in 2015 with a runtime of 110 minutes.","next_step":"Consider promoting the movie's genre mix and release year to attract a wider audience."}	Passed	Pass

Interpretation
=====
For all three feature-vector inputs, the machine learning pipeline successfully predicted the movie revenue class and its associated probability. These predictions were passed to the LLM, which generated structured JSON explanations describing the prediction, confidence level, key contributing factors, and recommended next actions. Every response was successfully parsed as valid JSON and passed schema validation, confirming that the prompt design consistently produced machine-readable outputs. In addition, all three inputs passed the PII guardrail because they contained only movie-related feature values and no personally identifiable information.

Conclusion
========
The complete Track C pipeline operated successfully from end to end. The saved Random Forest model generated reliable predictions, the LLM produced valid structured explanations, the JSON schema validation ensured output consistency, and the PII guardrail prevented unsafe inputs from reaching the LLM. This demonstrates that the integrated ML–LLM solution is capable of generating consistent, validated, and explainable predictions suitable for practical deployment.

Temperature Comparison (Track C)
=====
To evaluate the effect of temperature on the LLM responses, each of the three movie feature-vector inputs was processed twice using the same prompt: once with temperature = 0 and once with temperature = 0.7.

Temperature Comparison Results
======
Input	Output at Temp = 0	Output at Temp = 0.7	Key Difference

Input 1	The explanation identified the Action, Adventure, Fantasy, Science Fiction, and Thriller genres, with the Thriller genre highlighted as the second reason.	The explanation contained the same prediction and confidence level, but the wording of the second reason was slightly more descriptive.	Both outputs predicted High Revenue, but the temperature 0.7 response used more natural and expressive language while preserving the same meaning.

Input 2	The explanation highlighted the Comedy, Drama, and Romance genre combination and the movie's release year.	The explanation used shorter labels ("Genre" and "Release Year") and provided a broader marketing recommendation.	The prediction remained unchanged, but the wording became more flexible and less deterministic at temperature = 0.7.

Input 3	The explanation focused on the Crime, Drama, Mystery, and Thriller genres along with the release year and runtime.	The explanation expanded the reasons by emphasizing audience appeal and describing the runtime as beneficial for engagement.	Both outputs produced the same prediction, while temperature = 0.7 generated a richer and more detailed explanation.

Why Temperature = 0?
====
For this project, temperature = 0 was selected because the objective is to generate consistent, reproducible, and structured JSON explanations. At a temperature of 0, the LLM always selects the highest-probability next token, producing deterministic outputs for identical inputs. This behavior minimizes variation in wording, improves JSON consistency, and simplifies schema validation.

In contrast, temperature = 0.7 introduces controlled randomness by sampling from a broader probability distribution. As demonstrated in the experiments, the predicted class and confidence level remained unchanged, but the wording, supporting reasons, and recommendations became more varied and descriptive. Although this produces more natural-sounding explanations, it can also introduce inconsistencies in structured outputs. Therefore, temperature = 0 was selected for the final implementation because it provides reliable, repeatable, and schema-compliant JSON responses suitable for automated machine learning explanation pipelines.



End-to-End Feature Demonstration (Track C)
====
The complete prediction explanation pipeline was executed on three manually created movie feature vectors. For each input, the machine learning model generated a prediction and prediction probability, the LLM produced a structured JSON explanation, the response was validated against the predefined JSON schema, and the PII guardrail was executed before every API call.

End-to-End Results
===
Input	LLM Output	Valid JSON	Pass/Block
===
Input 1	{"prediction_label":"High Revenue","confidence_level":"high","top_reason":"The movie belongs to popular genres like Action, Adventure, Fantasy, and Science Fiction.","second_reason":"The movie falls under the Thriller category, which often attracts a large audience.","next_step":"Consider investing in marketing campaigns to capitalize on the movie's potential success."}	Passed	Pass

Input 2	{"prediction_label":"High Revenue","confidence_level":"medium","top_reason":"The movie is a Comedy, Drama, and Romance genre combination.","second_reason":"The movie was released in 2018.","next_step":"Consider promoting the movie to target the audience interested in Comedy, Drama, and Romance genres."}	Passed	Pass

Input 3	{"prediction_label":"High Revenue","confidence_level":"high","top_reason":"The movie has elements of Drama, Crime, Mystery, and Thriller genres.","second_reason":"The movie was released in 2015 with a runtime of 110 minutes.","next_step":"Consider promoting the movie's genre mix and release year to attract a wider audience."}	Passed	Pass

Structured Output Validation Results
===
Feature Input	Predicted Class	Probability	Validation Status
=
Input 1	1 (High Revenue)	0.9540	Passed
Input 2	1 (High Revenue)	0.6753	Passed
Input 3	1 (High Revenue)	0.8470	Passed

Interpretation
=======
For each of the three manually created movie feature vectors, the saved Random Forest pipeline successfully predicted the movie revenue class and the associated prediction probability. These values were incorporated into structured prompts and sent to the LLM. The LLM generated well-formed JSON explanations containing the prediction label, confidence level, key supporting reasons, and recommended next steps. Every response was successfully parsed using json.loads() and validated against the predefined JSON schema using jsonschema.validate(), confirming that the responses followed the required structure.

In addition, all three requests successfully passed the PII guardrail because they contained only movie-related feature values and no personally identifiable information. As a result, every request was safely processed by the LLM without being blocked.

Conclusion
=========
The complete Track C implementation operated successfully from end to end. The machine learning pipeline generated accurate prediction classes and probabilities, while the LLM transformed these predictions into structured, human-readable JSON explanations. All outputs passed schema validation, and the PII guardrail correctly allowed only safe inputs to be processed. This demonstrates that the integrated Machine Learning + LLM solution is capable of producing secure, reliable, explainable, and machine-readable predictions, making it suitable for deployment in explainable AI applications.

Summary
=====
All three feature-vector inputs successfully passed the PII guardrail because they contained only movie-related features and no personally identifiable information. The LLM returned valid JSON responses for each input, and all responses successfully passed schema validation. This demonstrates that the complete pipeline—from model prediction, through prompt construction, LLM explanation generation, JSON parsing, and schema validation—works correctly and reliably for the selected Track C implementation.


Part 4 extended the machine learning solution by integrating a Large Language Model (LLM) to provide structured, human-readable explanations for movie revenue predictions. A secure API connection was established using environment variables, and a reusable call_llm() function was implemented to communicate with the LLM. A zero-shot prompt was carefully designed to ensure the model returned consistent JSON explanations containing the prediction label, confidence level, supporting reasons, and recommended next steps.

The best-performing Random Forest pipeline developed in Part 3 was loaded using joblib.load(), and three manually created movie feature vectors were passed through the model to generate prediction classes and probabilities. These predictions were incorporated into structured prompts and submitted to the LLM. The returned responses were parsed using json.loads() and validated against a predefined JSON schema using jsonschema.validate(), ensuring that every explanation followed the required structure.

To improve application security, a regex-based PII guardrail was implemented to detect email addresses and phone numbers before every API request. Requests containing personally identifiable information were blocked, while valid movie-related inputs were processed successfully. Temperature experiments demonstrated that temperature = 0 produced deterministic and reproducible JSON outputs, whereas temperature = 0.7 generated more varied wording while preserving the same prediction. Finally, the complete end-to-end pipeline successfully combined machine learning predictions with LLM-generated explanations, producing reliable, validated, and machine-readable outputs suitable for explainable AI applications.
