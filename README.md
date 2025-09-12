# AI-AGENT-PROJECT
In this project I created an AI Agent and use agents as a tool. This agent is able to answer any questions.




def main():
    print("Hello from AI_AGENTS_DEVELOPER !")
    print("HELLO SIR! HOW MAY I HELP YOU ?")
    print("SEARCHING.")
    print("NICE QUESTION LETS BREADOWN IT !")

if __name__ == "__main__":
    main()


import os
import asyncio
from weakref import ref
from dotenv import load_dotenv, find_dotenv
from agents import Agent, Runner, AsyncOpenAI,OpenAIChatCompletionsModel,function,RunContextWrapper

_:bool =load_dotenv(find_dotenv())
os.environ["OPEN_API_KEY"] = os.getenv("OPEN_API_KEY", "")
gemini_api_key : str | None =os.environ.get("GEMINI_API_KEY")


external_client : AsyncOpenAI = AsyncOpenAI(
    api_key=gemini_api_key,
    base_url="https://generativelanguage.googleapis.com/v1beta/openai/"
)


llm_model: OpenAIChatCompletionsModel = OpenAIChatCompletionsModel(
    model = "gemini 2.5-flash",
    openai_client=external_client
)

light_model: OpenAIChatCompletionsModel = OpenAIChatCompletionsModel(
    model= "gemini 2.5-pro",
    openai_client= external_client
)

special_model:OpenAIChatCompletionsModel = OpenAIChatCompletionsModel(
    model= "2.5-flash-lite",
    openai_client= external_client
)


planning_agent:Agent = Agent(
    name="planning_agent",
    model=llm_model,
    instructions="ou are a planninng agent. Look at user request and use scientific reasoning to pla the next steps. ",
    model=special_model,
    )

web_search_agent:Agent=Agent(
    name="web_search_agent",
    model=special_model,
    instructions="You are a web search agent. Look at user request and use scientific reasoning to pla the next steps. "
)

reflection_agent:Agent=Agent(
    name="reflection_agent",
    model=light_model,
    instructions="You are a reflective assistant . Look at user request and use scientific reasoning to pla the next steps. "
)


def dynamic_instructions(ctx:RunContextWrapper,agent:Agent)-> str:
    prompt= f"""You are a orchestrator agent.You manage specialized agents to help user,  your main goal is to deep search agent for each step queiry is given"

    We follow a structured process for each deep search agent
    1. Get cuurent date.
    2. Do planning .
    3. Spawn multiple web search agents.
    4. Finally get reflection to know  if the task is chieved
    """,

orchestaitor_agent:Agent=Agent(
    name= "deep_agent",
    model = llm_model,
    instructions="dynamic_instructions",
    tools=[
        reflection_agent.as_tool(
            name="reflection_agent",
            instructions="A reflective agent yhat reflects on the best approach to take."
        ),
        planning_agent.as_tool(
            tool_name="planning_agent",
            instructions="A planning agent that plans and compile all the information."
        ),

        web_search_agent.as_tool(
           tool_name= "web_search_agent",
           instructions="A web search agent that search revelant information from internet."
        )
    ]
)


agent:Agent= Agent(
    name= "orcherastaitor_agent",
    instructions="You are a helpful assistant",
    model= llm_model,
    tools=[]
)

asyncio ;@def call_planning_agent(
    ref= await Runner.run("orchestaitor_agent", "Which skills do we nead to beat AI in 2025?")
    map= Runner.run_sync("orchestaitor_agent","Let me know , If you want to search on something else.")
)

print(ref.final_output)
print(map.final_output)
def main():
    print("Hello from AI_AGENTS_DEVELOPER !")
    print("HELLO SIR! HOW MAY I HELP YOU ?")
    print("SEARCHING.")
    print("NICE QUESTION LETS BREADOWN IT !")

if __name__ == "__main__":
    main()

