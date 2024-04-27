
### Setting Up Remote LLM Server
##### Author: Debojyoti Dey

Why vllM over other options: OpenAI Interface 

1. Instance > Deep Learning AMI
2. Create virtual environment 
3. Install vllm
4. 3 ways
    a. Jupyter/IPython 
    b. Serving Model 
    c. Using OpenAI api

##### a. Jupyter/IPython 
```
from vllm import LLM, SamplingParams

prompts = [
    "Hello, my name is",
    "The president of the United States is",
    "The capital of France is",
    "The future of AI is",
]
sampling_params = SamplingParams(temperature=0.8, top_p=0.95)
```
[List of Models](https://github.com/vllm-project/vllm)
```
llm = LLM(model="facebook/opt-125m")

outputs = llm.generate(prompts, sampling_params)

# Print the outputs.
for output in outputs:
    prompt = output.prompt
    generated_text = output.outputs[0].text
    print(f"Prompt: {prompt!r}, Generated text: {generated_text!r}")
```

##### b. Serving Model
1. ssh to the VM: ssh -i ""/Users/tatinidey/Documents/DJ/GitProjects/awsAccessKeys/code-server.pem"" ubuntu@ec2-52-91-43-250.compute-1.amazonaws.com


2. Get in the virtual environment and start the server
```
##### GPU restriction
###### This will tie to 127.0.0.1
poetry run python -m vllm.entrypoints.openai.api_server --host 127.0.0.1 --port 8000 \
--gpu-memory-utilization 0.6 --model facebook/opt-125m 

###### *** Check for the Security Group


###### This will be open to VM IP
poetry run python -m vllm.entrypoints.openai.api_server --port 8000 \
--gpu-memory-utilization 0.6 --model facebook/opt-125m 

curl http://34.207.175.151:8000/v1/models


```
3. To Test in Local Command Prompt: 
This should give the model name and aother information
```
curl http://127.0.0.1:8000/v1/models
```
4. To access outside the VM need to add the port to the Security Group.
With Options: Custom TCP > Any IP and Port:8000 
5. Check in the local command prompt
```
curl http://127.0.0.1:8000/v1/models
```

##### Using OpenAI API

```
from openai import OpenAI

# Modify OpenAI's API key and API base to use vLLM's API server.
openai_api_key = "EMPTY"
openai_api_base = "http://127.0.0.1:8000/v1"
client = OpenAI(
    api_key=openai_api_key,
    base_url=openai_api_base,
)
completion = client.completions.create(model="facebook/opt-125m",
                                      prompt="San Francisco is a",
                                      stream=False)
print("Completion result:", completion)


```