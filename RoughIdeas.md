I am writing this note not to forget about ideas that came up for the GeoDescription Project (I just arbitrarily named like this for convenience).

# Structure of NLU (Parse the key information)
- Parsing structure --> Slots: North/South/East/West,NorthEast,NorthWest,SouthEast,SouthWest
- Named Entity Recognition --> Here, NER is only used for the country names
 (Like Maike tackled, recognizing a correct country name is important) 
  - Country ID and then map them to candidate country names? EUR001- Spain, Espana, Kingdom of Spain..
   (NB! Espana in spanish pronunciation might not be recognized..)
   I think I saw a paper about parsing the same names earlier (A is a B, A is also called B...), there must be a way.
- Sentiment Analysis --> Sentiment in text level does not play that big role in this game.
- Intent classification --> To categorize intents e.g. ask, ask_confirm, answer, thank, sorry, praise, complain, etc..
- Segmentation --> This is related to intent classification.
- POS tagging --> POS tagging helps understanding the structure. So this is only used for the other NLP subtasks, but this should not be applied directly to the NLU module.

# Dialogue Manager
I need a better explaination of the current plan, but here comes just rough ideas.
As far as I understood, the system is trained on pairs of annotated dialogue and then
user gives the system feedback corresponding to the robots answer to a given problem.

## State tracking
- Dialogue history = Slot changes of the each game, (S, A, R, S')  
  
  Example Game  
  
  H_(t-2) : What is the name of country on the North West Side of China?  
  R_(t-2) : Kazakhstan.  
  H_(t-1) : A little more south of Kazakhstan and shares the border with China?  
  R_(t-1) : Tajikistan.  
  H_t : Incorrect. A bit more north of Tajikistan.  
  
  ```
  correct_answer_at_g1 = 'Kyrgyzstan'
  g1 = {(t-2)_H: ('GameStart', [Ask], 0, {Direction:[NW], RefPoint:[China], Answer:[]}), 
        (t-2)_R: ({Direction:[NW], RefPoint:[China], Answer:[]}, [Answer], -10, 
                  {Direction: [NW], RefPoint:[China], Answer:[Kazakhstan]}),
        (t-1)_H: ({Direction: [NW], RefPoint:[China], Answer:[Kazakhstan], [Inform], -15, 
                  {Direction:[S], Refpoint:[Kazakhstan, China], Answer:[]}),
        (t-1)_R: ({Direction:[S], Refpoint:[Kazakhstan, China], Answer:[], [Answer], -25, 
                  {Direction: [S], Refpoint:[Kazakhstan, China], Answer:[Tajikistan]}),
        (t)_H: ({Direction: [S], Refpoint:[Kazakhstan, China], Answer:[Tajikistan]}, [Complain, Inform], -30,
                {Direction: [S,N], Refpoint:[Kazakhstan, China, Tajikistan], Answer:[]})
         }      
  ```

To overcome the limit of ASR, if the system categorize a word 'Kajakistan' as a country name with NER module,
we can make it as phonetic information to find the 'Kajakhstan' with simple Levenstein distance in phonetic letters.
(e.g. Soundex algorithm)

## Reward calculation
TBA

## Action selection
Inference of epsilon greediness to avoid from not answering and make a candidate of dull responses and prevent them by using
Ease of response equation from the first paper(Jiwei Li).  
If action is Answer, we can set a list of candidate country list and make the system choose one of them.
If action is RequestInformation, we have to decide what feature the system can ask about.
