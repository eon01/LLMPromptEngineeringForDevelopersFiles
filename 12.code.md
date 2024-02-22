# ReAct: Reason + Act


## What is it?


## ReAct using LanChain


```text
# Question 1
Roden Cutler House is owned by an electricity infrastructure company that is owned by the Government of New South Wales, Australia, and was formed in what year?

# Question 2
Who was born first, Alan Paton or Jaroslav Seifert?

# Question 3
Anna Popplewell played Lady Lola in a series that follows what ?
```


```python
from langchain import OpenAI, Wikipedia
from langchain.agents import initialize_agent, Tool
from langchain.agents import AgentType
from langchain.agents.react.base import DocstoreExplorer

# Wikipedia is the docstore that the agent will use to answer questions
docstore=DocstoreExplorer(Wikipedia())

# Tools are the functions that the agent can use to answer questions
tools = [
    Tool(
        name="Search",
        func=docstore.search,
        description="useful for when you need to ask with search"
    ),
    Tool(
        name="Lookup",
        func=docstore.lookup,
        description="useful for when you need to ask with lookup"
    )
]

# Define the agent that will be used to answer questions
llm = OpenAI(temperature=0, model_name="text-davinci-002")

# Initialize the agent
react = initialize_agent(
    tools, # List of tools this agent has access to.
    llm, # Language model to use as the agent.
    agent=AgentType.REACT_DOCSTORE, # Agent type to use. If None and agent_path is also None, will default to AgentType.ZERO_SHOT_REACT_DESCRIPTION.
    verbose=True # Print out the agent's actions
    )

question = input("Ask a question: ")
react.run(question)
```


```text
> Entering new AgentExecutor chain...
Thought: I need to search Roden Cutler House, find the electricity infrastructure company that owns it, then find the year it was formed.
Action: Search[Roden Cutler House]
Observation: Roden Cutler House is a skyscraper in Sydney, New South Wales, Australia. It consists of 19 floors primarily office/commercial space. Construction finished in 1975 and it is located at 24 Campbell Street, Sydney. The Antenna height is 112m and the structure height is 108m. The building is owned by Ausgrid and the lower floors house the City South substation.
The building is named after Sir Roden Cutler, the longest-serving Governor of New South Wales and a Victoria Cross recipient. Roden Cutler won the Victoria Cross in the 1941 Syrian campaign where he was wounded, requiring the amputation of a leg.[1]
Thought: Roden Cutler House is owned by Ausgrid. I need to find the year it was formed.
Action: Search[Ausgrid]
Observation: Ausgrid is an electricity distribution company which owns, maintains and operates the electrical networks supplying 1.8 million customers servicing more than 4 million people in Sydney, the Central Coast and Hunter regions of New South Wales, Australia. It was formed in 2011 from the previously state-owned energy retailer/distributor, EnergyAustralia, when the retail division of the company, along with the EnergyAustralia brand, was sold by the Government of New South Wales, and the remainder renamed Ausgrid.
Thought: Ausgrid was formed in 2011. So the answer is 2011.
Action: Finish[2011]

> Finished chain.
```


```text
> Entering new AgentExecutor chain...
Thought: I need to search Alan Paton and Jaroslav Seifert, and find who was born first.
Action: Search[Alan Paton]
Observation: Could not find [Alan Paton]. Similar: ['Alan Paton', 'David Paton', 'Sunday Times CNA Literary Awards', 'Lost in the Stars', 'The Alan Parsons Project', 'Deaths in 1988', 'Cry, the Beloved Country', 'Jonny Steinberg', 'Fred Khumalo', 'Liberalism in South Africa']
Thought: Alan Paton was born on January 11, 1903. I need to search Jaroslav Seifert next and find its date of birth.
Action: Search[Jaroslav Seifert]
Observation: Jaroslav Seifert (Czech: [ˈjaroslaf ˈsajfr̩t] (listen); 23 September 1901 – 10 January 1986) was a Czech writer, poet and journalist. Seifert was awarded the 1984 Nobel Prize in Literature "for his poetry which endowed with freshness, sensuality and rich inventiveness provides a liberating image of the indomitable spirit and versatility of man".
Thought: Jaroslav Seifert was born on September 23, 1901. 1903 (Alan Paton) < 1901 (Jaroslav Seifert), so Jaroslav Seifert was born first.
Action: Finish[Jaroslav Seifert]

> Finished chain.
```


```text
> Entering new AgentExecutor chain...
Thought: I need to search Anna Popplewell, find the series she played Lady Lola in, then find what the series follows.
Action: Search[Anna Popplewell]
Observation: Anna Katherine Popplewell (born 16 December 1988) is an English actress. Popplewell is known for playing Susan Pevensie in the fantasy film series The Chronicles of Narnia (2005–2010), that earned her a number of accolades.
Aside from her The Chronicles of Narnia role, Popplewell played the role of Chyler Silva in the web series Halo 4: Forward Unto Dawn (2012) which is based on the video game of the same name, and starred as Lady Lola in the historical romantic drama series Reign (2013–2016), which was her first leading role in a television series.
Thought: Anna Popplewell played Lady Lola in the historical romantic drama series Reign (2013–2016). So the series follows a historical romantic drama.
Action: Finish[historical romantic drama]

> Finished chain.
```