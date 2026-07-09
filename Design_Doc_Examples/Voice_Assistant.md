# Voice Assistant

## I. Problem definition

### i. Origin

We are a development team at a B-bank, implementing innovative technologies to improve the efficiency and quality of clients service. Our goal is to improve the interaction between personal managers(PM) and clients, making this process more efficient, accurate and personalised.

The current voice assistant project aims to enhance communication quality between personal managers and clients by

- monitoring conversations in real-time;
- analysing tone;
- compliance monitoring of meeting goals;
- providing prompts as needed (retrieving relevant information from the knowledge base when managers face challenging questions).

After each interaction, the assistant generates a summary for CRM integration. Our objective is to improve service quality, streamline workflows, and support successful client outcomes.

### ii. Relevance & Reasons

#### ii.i Existing flow analysis

In the current communication flow, the responsibility for analysing the tone of the dialogue and ensuring compliance with meeting objectives rests entirely with the manager.

- The manager adjusts the conversation based solely on personal judgement.
- If the manager encounters a question they cannot immediately answer, they ask the client to wait (during which music plays on the client's phone) while they either consult an expert or search for the answer independently in the bank's knowledge base.
- After the meeting, the manager writes a summary in free form and manually uploads it to the system.

#### ii.ii. How much does a company lose when a manager's poor decision about the tone or direction of a dialogue, or making a client wait too long for an answer, impacts the outcome?

Analysis of clients feedback and churn indicates that the bank loses approximately 1% of its clients annually due to poor service. This results in lost sales estimated at 146 million rubles per year, primarily due to managers' delays in providing urgent information. Additionally, managers spend an average of 3 hours daily compiling meeting reports(summary), which translates to about 900 hours per month across the bank. Implementing a voice assistant is expected to reduce report preparation time from 3 hours to 30 minutes per day, significantly freeing up managers resources.

#### ii.iii Other reasons

Some additional advantages of voice assistant integration

- Cut new employee training time by 10% by analysing successful meetings to identify best practices and provide targeted recommendations for new employees.
- Enhance call centre service quality to improve overall service standards
- Increase Net Promoter Score(NPS) by 5-10 Points: Increase clients satisfaction and loyalty through improved, personalised service.
- Establish a technologically advanced brand, position the bank as innovative and forward-thinking, attracting clients interested in modern and effective solutions.
- Simplify personal managers' work

#### ii.iv Anti Goals

We may encounter opinions that we did not intend to implement. It is crucial to address several key points.

- The voice assistant is designed to support and help personal managers, not replace them. It should complement the manager's role by enhancing their capabilities rather than substituting their presence or decision-making.
- We don't prioritise quality over speed. The assistant should focus on delivering high-quality summaries and insights. Achieving fast summary generation or fast question answering is not a priority if it compromises the depth and accuracy of the information. Accept longer processing times in some cases if they lead to more detailed and valuable outputs.

#### ii.v. Previous work

Dion is a corporate social video network. The main functionality is video and audio communications, conferences of up to 5000 users.

Our case requires solutions for speech-to-text recognition, sentiment analysis, text summarization for speech recognition, monitoring the tone of the dialog and forming the meeting summary. For prompting the manager during the dialog and correctness of following the goals of the meeting, the functionality of large language models with the information about the subject of the meeting and the interlocutor provided before the meeting.

The tasks of summarization and sentiment analysis are now successfully handled by pre-trained LLMs. Pre-training is more often performed on a dataset that is not tied to the subject domain.

The voice-to-text transcription function is currently already provided in Dion.

It can be assumed that the voice assistant feature during a call will be popular among users of the platform, whose core functionality is precisely conferences and calls. If the quality of the feature is not at a high level, it could lead to serious consequences, as it will cause problems in business conversations of clients. The bar for the quality of service before the introduction of the extension is quite high.

In the future, the functionality of the voice assistant may change along with the development of the Dion platform itself, so it is worth allowing for scalability of the voice assistant.

Given the factors that require an advanced and flexible solution with scalability prospects, I am inclined to think that we should develop our own voice assistant service using existing LLM and speech-to-text recognition models.

#### ii.vi. Other issues & Risks

We may encounter problems during the development or usage of the voice assistant

1. Existing solutions may not cope well with our data. Pre-trained models might not generalise effectively to our specific use cases, leading to inaccurate results.
2. Existing solutions may complicate the manager's work. Introducing new tools and processes could overwhelm managers, increasing their cognitive load.
3. The learning curve for managers to effectively use the new system may be steep, reducing productivity in the short term.
4. Critical elements of the system may not be ready on time. Dependencies on third-party technologies or internal resources may cause delays in delivering key features. Unforeseen technical challenges, such as issues with natural language processing accuracy or real-time performance, could hinder progress.
5. The system may not pay for itself in real life. The return on investment (ROI) might be lower than expected if the system fails to significantly improve efficiency or clients satisfaction. The upfront costs, including development, deployment, and training, may not be offset by the anticipated savings or revenue increases.

## II. Metrics and losses

The proposed system consists of multiple components that need to be evaluated both individually and as an integrated solution. We will decompose the system into logical modules and outline the quality assessment methodologies for each component. Most evaluations will require labelling by assessors. While some tasks can be handled by individuals without specialised knowledge,for others, experts familiar with the subject area will be required. To mitigate the need for external specialists and follow NDA, we may utilise our personal managers for labelling certain tasks.

### i. Voice to text metrics

#### i.i. Evaluation Approach

Given that we're utilising functionality developed by another team, the selection of the loss function falls under the responsibility of those responsible for training those models. Our primary focus is on evaluating how effectively the model handles our specific dataset.

To assess the quality of voice-to-text translations and determine the most suitable option, we propose a straightforward yet efficient approach. Engage assessors or managers to review the generated translations.

Selection process:

- For high-quality translations: Choose the best option among the provided alternatives.
- For low-quality translations: Leave all options unselected if the meaning is not accurately conveyed across any of them.

This method reduces the evaluation time as it eliminates the need for individual word-level marking.

#### i.ii. Metric Calculation

The chosen metric for evaluation will be the overall accuracy of the selected answers. This approach ensures a comprehensive assessment of the model's performance across various aspects of the translation process.

### ii. Sentiment analysis metrics

We intend to leverage existing pre-trained models for sentiment analysis instead of developing custom models from scratch. Our primary objective is to select the optimal model capable of detecting sentiment directly from audio input, eliminating the need for intermediate text translation.

#### ii.i. Sentiment accuracy

We can calculate the percentage of correctly identified main tones of a dialogue (positive, neutral, and negative tones). Final metric combining accuracies of different tones in the dialogue with different weights due to negative tone more important than others.

Negative tone identification crucial for:

- Clients dissatisfaction
- Managerial non-compliance with clients communication standards

Main tones of dialogue we already have from personal managers' reports.

#### ii.ii. Online sentiment accuracy

To detect the tone of dialogue in real-time, not just in the final report, we should evaluate quality continuously. Similarly, we can utilise the accuracy of tone detection by implementing a sliding window approach.

- Evaluate sentiment every 60 seconds
- Cover the last 120 seconds of dialogue

This approach balances complexity and practicality because we don't evaluate each second of dialogue

Moreover we need to get labelled data with timecodes from assessors(without technical expertise) because we don't have this dataset.

Additionally, we require labelled data with timecodes from assessors (technical expertise is not required). This is necessary because we currently don't have this dataset.

Possible labelling strategy:

- Identify tone changes (e.g., from neutral to positive or from negative to positive)
- Record time codes of tone shifts
- Provide single assessment for consistent tone throughout dialogue

The final metric for sentiment detection will be a weighted average of the accuracies of different tones in the dialogue.

This approach provides a comprehensive view of the model's performance across all relevant sentiment categories, allowing for a nuanced evaluation of its effectiveness in real-world applications.

### iii. Topic identification metrics

We need to develop a classification model to automatically identify key topics within a dialogue and compare them to predefined meeting topics. The evaluation approach will be similar to that used for dialogue sentiment analysis, with sliding window thresholds increasing to 60 and 360 seconds, respectively. While our goal is to detect topics in real-time, final reports will include all topics discussed during the dialogue. To achieve this, we will require managers to label the dialogue with time codes indicating when topics change.

### iv. QA metrics

To address this challenge, we aim to develop a Retrieval-Augmented Generation (RAG) model that can effectively locate and incorporate relevant information from our knowledge base in response to manager queries. The primary metric for evaluating this model's performance will be Retrieval Precision, which measures the accuracy with which the model extracts necessary pieces of information from the knowledge base in response to client requests. This metric will be assessed by directly comparing the relevance of the retrieved fragments to the specific client inquiry. To further refine our evaluation process, we will compare the model's outputs against predefined client requests, measuring the alignment between the model's responses and the expected outcomes. For instance, if a client asks about product pricing strategy, the model's success will be determined by whether it accurately retrieves relevant pricing-related data while avoiding unrelated or redundant information.

### v. Summary metrics

Same as speech to text metrics. Engage managers to review the generated summary to choose the best model by the percentage of votes.

### vi. System metrics

We have to evaluate voice assistance not only by component, we need to evaluate the quality of the system at all.

#### vi.i. Client NPS, CSAT

Evaluate clients satisfaction following a dialogue with a manager and their likelihood to recommend our service. This is measured through a straightforward survey where clients rate their experience on a scale from 1 to 10. High ratings indicate that clients are pleased with their interaction, serving as a critical measure of the manager's effectiveness.

#### vi.ii. Managers Feedbacks

An assessment provided by managers regarding the quality of the system after a conversation. This evaluation may cover aspects such as system performance, the quality of the information delivered, and the client interaction process. Manager feedback is crucial for identifying potential issues and areas for improvement, which can enhance system performance, manager efficiency, and subsequently client satisfaction.

#### vi.iii. Time to resolution

The duration of the dialogue between the client and the manager, required to resolve the client's issue, measures how long it takes to fully address and satisfy the client's request. Reducing this resolution time can enhance manager efficiency and boost client satisfaction.

Additionally, we can calculate the percentage of issues resolved quickly, such as those addressed within a specified timeframe (e.g., under 5 minutes). It's important to separately track complex cases to analyse lengthy interactions and identify opportunities for system improvement.

#### vi.vi. Reporting Time

The time required for a manager to create and send a report based on a client dialogue measures the efficiency and ease of the reporting process. This metric helps evaluate how streamlined and effective the reporting workflow is. Reducing the time spent on reporting enables managers to allocate more time to client interactions, thereby enhancing overall productivity.

#### vi.v. Conversion rate

The percentage of client interactions that result in a successful transaction or achievement of a target action, such as a product purchase or contract signing. A high conversion rate reflects the effectiveness of managers in negotiating and guiding clients toward completing desired actions.

## III. Dataset

The atomic object of the dataset for the voice assistant is a bundle of (client interaction, timestamp, intent), and the target variable we aim to predict is the accuracy of intent recognition and subsequent action execution.

### i. Data Source

#### i.i. Interaction History

This source includes logs of previous interactions between clients and the voice assistant. It captures data such as recognized commands, corresponding responses, session duration, detected intents, and any errors encountered during processing. All of this information is stored in a centralised database.

Key Elements: Recognized intent, command transcription, client response, session ID, and feedback (e.g., whether the command was correctly understood).

#### i.ii. Voice Command Data

This dataset consists of historical voice commands issued by clients, including both the raw audio files and their transcriptions. This data helps in refining the accuracy of the voice recognition system.

Key Elements: Raw audio, transcription, background noise level, voice pitch, and speaker ID.

#### i.iii. Contextual Metadata

This includes metadata related to each interaction, such as the device used, location coordinates, time of interaction, and the level of environmental noise. This information helps in understanding the context in which commands were issued, which can be critical for improving response accuracy.

Key Elements: Device ID, location coordinates, time of interaction, environmental noise level.

#### i.iv. Client Profiles

Client profiles contain personalised data such as the Client ID, command history, language preferences, and frequently accessed services or apps. This information enables the voice assistant to tailor responses and improve client experience.

Key Elements: Client ID, command history, language preference, frequently accessed services or apps.

### ii. Data Labelling

To enable real-time data detection and enhance the quality of voice assistant responses, we need to create a labelled dataset with the assistance of assessors and/or personal managers.

Assessors will review each client request and the corresponding assistant response, evaluating them based on predefined criteria(instructions). If the response does not meet the expected standards, the assessor is responsible for providing a corrected answer that would better satisfy the clients’ request.

For offline sentiment analysis and topic identification, we already have labeled data derived from the final reports provided by managers. These labels will be used to train and refine the models for these specific tasks.

## IV. Validation Schema

Developing large-scale machine learning systems, such as a voice assistant, it's crucial to divide tasks into manageable subtasks and validate each one individually before integrating and testing the entire system. For our system, this approach involves dividing the work into subtasks with usage of pre-trained LLMs: ASR(automatic speech recognition), NLU(Natural Language Understanding), TTS(text-to-speech) and summarization

As for all the subtasks we will use pretrained models but as for topics of dialogues and particular prompts we need new data in a stream. So we need data with stop words to track them in order to activate the voice assistant.

### i. Requirements

- New data is coming daily.
- Data can arrive with a delay of up to 72 hours.
- Recent data is most probably more relevant for the prediction task because there can be local changes if apps or services do not work properly.

It is important to shuffle train and test data in order to dodge overfitting because in fact, a large enough neuron can remember the intonation and manner of speech of a particular person and will use this information for test recordings with this person. At the same time, recognition will work much worse on new people.

### ii. Inference

When we work with big ML systems as voice assistants we should not forget about cutting it on subsystems and work with them partially. As a result we have to unite all the subtasks and make end-to-end validation to prove all the parts work properly.

## V. Baseline Solution

Unfortunately we can't use really simple solutions like constant models due to system parts complexity.

### i. Model Baselines and Feature Baselines

For the tonal recognition task, we can create a dictionary of negative and positive words, count their frequency, and assign a positive or negative label to phrases based on their presence. Alternatively, we could employ pre-defined rules, such as assigning a negative label if there are more than two negative words in a sentence, or if over 20% of the words are negative, labelling the phrase/sentence as negative while the remainder is neutral.

The tonal recognition task essentially involves multi-class classification of sentences into three categories (negative, neutral, positive). This can be achieved using Naive Bayes or, for example, Logistic Regression. We can also utilise pre-trained models (such as those available in the Spacy package) that do not use deep learning methods. In some cases, we may not even need to convert speech to text; it may suffice to simply identify the emotional tone.

The task of summarising the entire meeting will likely not be solvable with classical ML models.

Feature baselines are described in the datasets section - as a baseline, it is advisable to take all data excluding historical data.

### ii. Deep Learning Baselines

#### ii.i Speech-to-text

For the speech-to-text conversion task, one can utilise third-party speech recognition APIs, such as Google Speech Recognition or its equivalent from Yandex. This software does not require engineering efforts. However, this option can be costly and exposes our data and conversations to another company, making it undesirable if the company is our counterparty. A simpler, albeit less effective, solution is to use open-source speech recognition libraries that can be installed locally. There are many available in Python, and not all of them utilise deep learning models. A more preferred option, which has higher latency but better accuracy, is to use open models from Hugging Face’s Automatic Speech Recognition (ASR), such as s2t from Facebook. It can be fine-tuned with the corporate terminology we need—ASR allows for this (here is a [guide](https://huggingface.co/learn/audio-course/ru/chapter5/fine-tuning)) . Overall, this approach should suffice

#### ii.ii Tone identification

For tonal recognition, it is advisable to use something simplified and classical, for instance, DistilBERT—these models are distilled and operate faster than conventional BERT. Naturally, we should opt for pre-trained models, as there is no point in retraining a model—the tone of a conversation does not depend on the subject matter or terminology. We can also utilise built-in NLTK functions for tone detection.

#### ii.iii Question answering baseline

In our system, providing accurate and efficient responses to clients queries is a key objective. To achieve this, we can employ Few-shot learning, where the model is fine-tuned with a small number of examples for specific tasks. However, given the extensive size of our knowledge base, Few-shot learning alone may not suffice for delivering fast and high-quality responses.

To address this, we propose using Retrieval-Augmented Generation (RAG) as our baseline strategy. RAG combines the strengths of retrieval-based models and generative models, making it particularly well-suited for our large-scale knowledge base. We can use BERT for summarising the best answer from the knowledge base. This approach enhances the model’s ability to generate accurate answers by leveraging relevant information retrieved from the knowledge base.

### iii. Baseline comparison

The increase in accuracy within this project will primarily be achieved by transitioning to a more advanced architecture. Moreover, in all tasks except for the manager's question answering, we utilise pre-trained models, some of which may require slight fine-tuning (for the summarization task). Therefore, the development time will not be extensive. The computation time also does not play a significant role— even in the question-answering task, we will likely have up to 30 seconds to think, which is enough for generating a response.


--

With increased model complexity, diminishing returns are often observed. This is because more complex models may only slightly improve results on validation data, yet they require significantly more time and resources for development and training. It is crucial to apply the concept of "early stopping" — to halt the model's training when the gain in accuracy becomes marginal compared to the increased labour costs. This can help to avoid scenarios where model development turns into a resource black hole.