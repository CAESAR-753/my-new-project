# my-new-project
Building AI 
larPost

Building AI course project

Summary

KlarPost turns official letters from German authorities into plain language. You take a photo of the letter, and the app tells you what it is about, what you have to do, and by when. Building AI course project.

Background

Letters from the tax office, the job centre, the health insurance fund or the immigration office are written in legal language that many people struggle with. Missing the point of such a letter is not just annoying, it can cost money or legal rights.

It is a common problem. In a 2024 survey of 2,039 people in Germany (Taxfix and Wortliga), 69 % said they have to read official letters several times, and 47 % said they need another person to help them.
It has real consequences. In the same survey, one in four people said they had already suffered a financial disadvantage because they did not understand an official letter, for example a late fee or a reduced benefit.
Some groups are hit much harder. According to the LEO 2018 study by the University of Hamburg, about 6.2 million German-speaking adults (12.1 % of 18 to 64 year olds) have low literacy. People who are still learning German and many older people face the same barrier.
The problem is old. A 2008 survey by the Gesellschaft für deutsche Sprache already found that 86 % of people have difficulties with the language used by authorities, courts and lawyers.

My personal motivation: [add one or two sentences here about why this topic matters to you].

The topic is interesting because the task is narrow and well defined. Most official letters follow a small number of patterns, and what the reader needs is always the same: what is this, what do I have to do, and what is the deadline?

How is it used?

The user is anyone who has just opened an official letter and does not know what to do with it. It is used at home, usually on a phone, and often in a stressful moment. That means the app has to be simple, calm, and usable by people who do not read well.

The user takes a photo of the letter or uploads a PDF.
The app reads the text and recognises what kind of letter it is.
The app shows a short result in plain language:
What is this? For example: "This is a payment request from the city."
What do I have to do? For example: "Transfer 148.50 Euro."
By when? For example: "By 30 October 2026."
What happens if I do nothing? For example: "You will get a reminder with an extra fee."
The user can tap on any difficult word to get an explanation, have the result read aloud, or switch to another language.
The app offers to save the deadline in the phone's calendar.

People who need to be considered:

Readers with low literacy or little German: short sentences, large text, read-aloud function, translations.
Older people: no account needed, very few buttons.
Helpers such as family members, social workers and volunteers in advice centres, who could use it to prepare a conversation.
The authorities themselves: the app must not put words in their mouth. The original letter always stays visible next to the explanation.

The small demo below shows the core idea with very simple methods: it guesses the type of a letter from keywords and pulls out the deadline and the amount.

python
import re

LETTER_TYPES = {
    "payment request": ["zahlen", "überweisen", "betrag", "mahnung"],
    "request for documents": ["nachweis", "unterlagen", "einreichen", "vorlegen"],
    "decision (Bescheid)": ["bescheid", "widerspruch", "bewilligt", "abgelehnt"],
    "appointment": ["termin", "erscheinen", "vorsprechen"],
}

def classify(text):
    text = text.lower()
    scores = {label: sum(text.count(word) for word in keywords)
              for label, keywords in LETTER_TYPES.items()}
    return max(scores, key=scores.get)

def find_deadlines(text):
    return re.findall(r"(?:bis zum|bis|spätestens am)\s+(\d{1,2}\.\d{1,2}\.\d{4})", text)

def find_amounts(text):
    return re.findall(r"(\d{1,3}(?:\.\d{3})*,\d{2})\s*(?:€|Euro|EUR)", text)

letter = """Sehr geehrte Frau Beispiel,
für das Jahr 2025 ist noch ein Betrag von 148,50 Euro offen.
Bitte überweisen Sie den Betrag bis zum 30.10.2026 auf das unten genannte Konto.
Sollte die Zahlung nicht fristgerecht eingehen, wird eine Mahnung versandt."""

print("Type of letter:", classify(letter))
print("Deadline:      ", ", ".join(find_deadlines(letter)))
print("Amount:        ", ", ".join(find_amounts(letter)), "Euro")

Output:

Type of letter: payment request
Deadline:       30.10.2026
Amount:         148,50 Euro

The example letter is invented. A real version would replace the keyword list with a trained classifier and the regular expressions with a model that also understands deadlines written in words, such as "within one month of receiving this letter".

Data sources and AI methods

The hardest part of this project is the data. Real official letters contain names, addresses, tax numbers and health information, so they cannot simply be collected from the internet.

Data	Where it comes from	Used for
Sample letters	Donated by volunteers and advice centres, with consent, and with all personal data removed	Training and testing the letter type classifier and the deadline extraction
Template letters and forms	Published by authorities on their own websites	Learning the typical structure and wording of each letter type
Pairs of standard and plain German	DEplain corpus by Stodden, Momen and Kallmeyer (the licence differs between the parts of the corpus and must be checked for each part)	Training and evaluating the simplification step
Legal terms and their meaning	Gesetze im Internet, the official collection of German federal law	Building the glossary behind the "tap a word" function

AI methods, step by step:

Step	Method
Read the photo	Optical character recognition, for example with the open source engine Tesseract
Recognise the letter type	Text classification. A simple start is a bag-of-words representation with tf-idf and a naive Bayes or nearest neighbour classifier, as covered in the Building AI course
Find deadlines, amounts and reference numbers	Information extraction: rules for the simple cases, a trained sequence model for the harder ones
Explain in plain language	Text simplification and summarisation with a neural language model, evaluated against human-written plain language
Check the result	A second step compares every date and amount in the explanation with the original text and blocks the answer if they do not match
Challenges
It is not legal advice. KlarPost explains what a letter says. It does not tell you whether a decision is correct or whether you should file an objection. For that, people still need an advice centre or a lawyer.
Mistakes are costly. A wrong deadline is worse than no help at all. Language models can produce fluent text that is wrong, so dates and amounts must always be checked against the original and shown together with the matching passage from the letter.
Privacy. The letters contain very sensitive data. Processing should happen on the device where possible, nothing should be stored without consent, and the project has to comply with the GDPR.
Uneven quality. The system will work best for common letter types and worse for rare ones, handwritten notes, bad photos or letters from small municipalities with unusual wording. It should say clearly when it is unsure instead of guessing.
It treats the symptom. The real solution would be for authorities to write clearly in the first place. A tool like this must not become an excuse to keep letters complicated.
Over-reliance. Users may trust the summary and stop reading the original. The interface should keep reminding them that the letter itself is what counts.
What next?
Build a first prototype for one narrow area, for example letters about the broadcasting fee or about housing benefit, and test it with real users in an advice centre.
Add more languages, starting with those most often needed in advice centres.
Add help with the answer: a draft reply or a checklist of documents to send.
Work with authorities, so that the same technology checks their letters for readability before they are sent.

To move on, the project would need people with experience in natural language processing, a lawyer for administrative law, a data protection expert, plain language translators, and a partner organisation such as an advice centre or a municipality that is willing to test it.

Acknowledgments
Project template: Building AI course by Reaktor Innovations and the University of Helsinki
Grotlüschen, A., Buddeberg, K., Dutz, G., Heilmann, L., Stammer, C. (2019): LEO 2018 – Leben mit geringer Literalität, University of Hamburg
Taxfix and Wortliga (2024): survey on the language of authorities, 2,039 respondents, conducted by Qualtrics
Gesellschaft für deutsche Sprache (2008): survey on legal and administrative language, conducted by the Institut für Demoskopie Allensbach
Stodden, R., Momen, O., Kallmeyer, L. (2023): DEplain: A German Parallel Corpus with Intralingual Translations into Plain Language for Sentence and Document Simplification, ACL 2023
The demo code and the example letter in this README were written for this project. No images, code or data from others are included in this repository.
1 früherer Schritt
Fakten für den Hintergrund prüfen
README.md schreiben
Demo-Code und README prüfen
Readme
MD
Demo
PY
Websuche
3 Suchen
