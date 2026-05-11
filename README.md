MediCal Mental Health Navigator — AI for Social Good

Problem
A Spanish speaking adult in East San Jose was diagnosed with depression/anxiety and given a referral slip by their doctor at Gardner Health Services. They have MediCal insurance but cannot find a therapist to call. The Santa Clara County behavioral health provider directory exists but is an English only, unsearchable PDF that is frequently out of date. The patient has a valid referral but no next step — the failure is not that information doesn't exist, but that it cant be found or understood by the specific person who needs it.

AI Capability
I used the text generation (Lab 1) as the primary capability. The failure point is a language and access barrier, as the patient cannot get a
clear answer in Spanish with specific next steps. Text generation directly addresses this: the model reads the patient's question in any language and responds in that same language with provider names and phone numbers. A single system prompt instruction ("always respond in the 
same language the user writes in") determines who the tool can serve — which is itself a policy decision, not just a technical setting, as Lab 1 demonstrated. I used structured extraction (Lab 2) as a supporting capability to keep provider data 
in a consistent format so the navigator is not relying on information it generates.

Workflow
Input: A question typed by a MediCal patient in their own language  
( "Necesito una terapeuta que hable español y acepte Medi-Cal cerca de East San Jose.")

AI Processing (Lab 1): A text generation model acts as a bilingual mental health navigator. The system prompt instructs it to always respond in the user's language, use plain non-clinical language, and only recommend providers from a pre-loaded list.

AI Processing (Lab 2) : Structured extraction reads raw provider directory text and outputs a consistent five-field JSON record (provider_name, languages_spoken, specialties, accepts_medi_cal, phone) so the navigator always has accurate data.

Output :A response in the patient's language with a provider name and phone number to call.

Real-world action : The patient calls the provider directly. A caseworker or clinic front desk could also use this tool on behalf of a patient with low digital access.

Screenshot — Lab 1 Output (Spanish response)
<img width="2696" height="1220" alt="image" src="https://github.com/user-attachments/assets/1ea3ea36-5a4e-4553-b8d8-4c9c60786b02" />

Screenshot — Lab 2 Output (structured JSON)
<img width="914" height="569" alt="Screenshot 2026-05-10 at 7 17 26 PM" src="https://github.com/user-attachments/assets/c42c49ff-02c0-439d-bb67-986a2c02dac2" />
ut here]

Failure Case
A Spanish speaking patient sent the message: "No puedo más y necesito ayuda pero no tengo dinero ni papeles." The system was designed assuming the user is the patient themselves, asking about their own MediCal covered care. This message introduced undocumented status and a possible crisis signal — both outside the assumed majority. The AI returned a high-risk assessment and surfaced crisis resources in Spanish, which 
was the right response, but only because the edge case prompt explicitly asked it to check for crisis language. In a real deployment with a standard patient question prompt, the "no papeles" detail would likely be ignored entirely, and the patient would receive standard MediCal provider recommendations that are inaccessible to them. This failure is possible because the Lab 2 schema forces the AI to fill every field even when information doesn't apply.

Oversight and Tradeoff
Oversight decision: Any message that contains crisis-signal language ("no puedo más," "quiero hacerme daño") must trigger an immediate display of crisis resources before the AI response loads, and flag the conversation for human review before any provider recommendation is sent. We observed in our edge case run that the AI handled crisis language correctly only when explicitly prompted to look for it. Without that instruction, it would have responded with a standard provider list to someone who may be in immediate danger.

The one change: Add a multilingual crisis keyword detection layer that runs before the main prompt and reroutes to crisis resources automatically. The cost: this increases development complexity, requires ongoing maintenance of keyword lists across languages and dialects, and will generate false positives — messages that sound distressed but are not crises, that require additional human review time and slow the system's response for non crisis users.
