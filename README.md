ProcessOS — AI Process 
Author: Shubham Kumar Singh
License: MIT



A workflow-engineering portfolio project built for the Camunda AI Process Forward Deployed Engineer role.

ProcessOS explores an end-to-end workflow deployment journey: describe a business process in natural language, probe its integrations, review a workflow blueprint, run a bounded Coder/Tester verification gate, and then explicitly authorize a local Camunda deployment. The bundled Rainbow Corp application acts as the example customer system for a refund workflow, including a human-review path and a simulated refund API.

Scope: This is an independent portfolio demo, not an official Camunda product and not affiliated with Camunda. The refund API is mocked; no real payment provider, bank account, or financial transaction is involved. The current deployment integration is designed around the bundled Rainbow Corp refund contract and is not a universal adapter for arbitrary applications.

What the project demonstrates
Discover before building: conversationally clarify the trigger, business rules, decisions, integrations, and expected outcome.
Probe before deploy: validate configured HTTP/API and WebSocket endpoints before allowing workflow generation to proceed.
Bounded build verification: generate and inspect a BPMN workflow through a Coder/Tester gate with a maximum of three iterations in the UI; deterministic checks cover process identifiers, required workflow structure, expressions, and worker-task types.
Camunda workflow integration: use a controlled local CLI path to validate runtime prerequisites and deploy a verified BPMN process to a local Camunda 8 development runtime.
Human-in-the-loop decisions: route refund requests above ₹5,000 to human review. Approval can continue to the mock refund task; rejection prevents refund execution.
Operational visibility: inspect requests, product/customer master data, a human-review queue, activity history, and live workflow status updates in the Rainbow Corp console.
Integration separation: keep customer-facing request intake separate from the workflow bridge, job workers, routing, and mock refund execution.
Architecture
Business request in plainlanguage
ProcessOS discovery
Endpoint and contract probes
Reviewed workflow blueprint
Coder / Tester gateup to 3 iterations
Local Camunda 8 runtime
Separate workflow bridge andjob workers
Rainbow Corp applicationFastAPI + SQLite + web UI
Human-review queue
Mock refund APIsynthetic result only
Pass + explicit authorization
Components
Component	Responsibility	Default local address
ProcessOS Studio	Discovery, endpoint probing, blueprint review, Coder/Tester gate, local Camunda control	http://127.0.0.1:8090
Rainbow Corp customer application	Refund-intake chatbot and request status	http://127.0.0.1:8000
Rainbow Corp admin console	Request register, product/customer master data, human-review queue, activity log	http://127.0.0.1:8000/admin
Mock Refund API	Returns a simulated refund result and synthetic transaction reference	http://127.0.0.1:8001
Local Camunda 8 runtime	Executes the deployed BPMN workflow in development	REST API: http://127.0.0.1:8080/v2
Workflow bridge	Dispatches requests, starts process instances, handles worker jobs and review decisions, syncs results	Local process; no public UI
Screenshots
The images below are embedded from the PNG files in this repository's root directory. They show the local demo UI and sample-data outcomes.

ProcessOS: discover, probe, verify, and deploy
Initial studio and build-gate views

Discovery	Build and verify	CLI control
		
Workflow generation and verification



Local Camunda gate and execution output



Rainbow Corp: refund intake and standard completion
Customer intake	Request summary	Simulated completion
		


Rainbow Corp: high-value request and human review
A high-value request is held for review rather than being refunded directly by the customer-facing app.

Review required	Workflow running	Pending review queue
		
Reviewer decision recorded	Refund after approval	Request status before workflow pickup
		
Rainbow Corp admin console


Repository contents
The repository includes the screenshots above and the source-code bundle Clean_ProcessOS_and_RainbowCorp.zip. Extract the bundle locally before running the applications. The bundle contains the ProcessOS project and the Rainbow Corp demo stack, including the local workflow bridge, BPMN model, mock refund API, and tests.

The extracted source layout is organized around these components:

PROCESS/
  Shubham_Singh_ProcessOS_Demo/
    main.py                    # ProcessOS FastAPI application
    processos_studio_agent.py  # Discovery, blueprint and validation logic
    processos_runtime.py       # Local runtime orchestration
    camunda_manager.py         # Local Camunda CLI/runtime management
    static/studio.html         # ProcessOS Studio UI
    tests/                     # ProcessOS tests

RAINBOW/
  Rainbow_Corp_Demo_Stack/
    rainbow_corp_app/          # Customer application, admin UI and SQLite state
    workflow_assets/           # Camunda BPMN process model
    local_bridge/              # Workflow bridge and job workers
    mock_refund_api/           # Simulated refund API
    tests/                     # Integration and component tests
Run locally
The bundle is intended for a local development environment. The commands below are for Windows PowerShell after extracting the ZIP and opening a terminal in the extracted repository root.

1. Configure the demo services
Copy each component's .env.example to .env and configure local values as described by that component's README. Do not commit .env files or real secrets.

Rainbow Corp app: configure INTEGRATION_API_TOKEN.
Mock Refund API: configure DEMO_REFUND_API_KEY and keep its local bridge configuration consistent.
ProcessOS Studio: configure OPENAI_API_KEY in its local environment if using the LLM-backed discovery flow.
Use separate, randomly generated local demo secrets. Never place production credentials, customer data, or private API keys in the repository, screenshots, or issue reports.

2. Start the Rainbow Corp services
Open separate PowerShell terminals from the extracted repository root:

cd RAINBOW/Rainbow_Corp_Demo_Stack/rainbow_corp_app
.\run.bat
cd RAINBOW/Rainbow_Corp_Demo_Stack/mock_refund_api
.\run_mock_api.bat
The customer app defaults to http://127.0.0.1:8000; the mock API health endpoint defaults to http://127.0.0.1:8001/health.

3. Start ProcessOS Studio
In another PowerShell terminal:

cd PROCESS/Shubham_Singh_ProcessOS_Demo
.\run.bat
Open http://127.0.0.1:8090. Follow the UI to describe the workflow, configure/probe endpoints, review the blueprint, and run the Coder/Tester gate. Deployment remains behind an explicit local control; review the target and workflow before authorizing it.

4. Verify the sample workflow
Submit a sample refund request in Rainbow Corp and observe the request state in the chat and admin console. Use a request above ₹5,000 to exercise the human-review path. The expected demo behavior is that the workflow controls routing and approval, while the refund service returns only a mock result.

The ProcessOS integration currently expects the bundled Rainbow Corp contract, including its configured authentication headers, /api/v1/refunds action endpoint, and known worker types. Passing the local tests does not by itself prove that another Camunda cluster or an arbitrary external API is configured and reachable.

Tests and validation
Run tests from each extracted project environment after installing that project's dependencies. For example:

cd PROCESS/Shubham_Singh_ProcessOS_Demo
py -3.11 -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements-dev.txt
.\.venv\Scripts\python.exe -m pytest -q
cd RAINBOW/Rainbow_Corp_Demo_Stack
py -3.11 -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r rainbow_corp_app/requirements.txt -r local_bridge/requirements.txt -r mock_refund_api/requirements.txt pytest
.\.venv\Scripts\python.exe -m pytest -q tests
Recorded test results for this bundle: ProcessOS — 28 passed; Rainbow Corp — 22 passed. These test results cover local automated checks; they are not a claim that a clean machine installation or a live external Camunda cluster has been independently verified.

Security and demo boundaries
Keep the web applications bound to 127.0.0.1 for local demonstration. The sample admin console is not intended to be an internet-facing production admin system.
Never commit .env files, API keys, OAuth secrets, runtime databases, or customer data.
Endpoint probes are intended to validate integration reachability and contracts, not to execute refund actions.
The mock refund API creates synthetic outcomes only. No real money is moved.
Names, orders, email addresses, timestamps, and transaction references shown in screenshots are sample demo data.
Why this is relevant to Forward Deployed Engineering
This project demonstrates a practical approach to the delivery loop described in the role: translate an ambiguous business request into an explicit process, validate the integration boundary before deployment, keep human decisions visible, gate execution on verification, expose operational status, and document a repeatable local setup. It also makes the current integration boundary and demo limitations explicit rather than presenting a sample implementation as a production-ready universal platform.

References
Camunda
AI Process Forward Deployed Engineer — role description
Camunda 8 CLI (c8ctl) getting started
Author
Shubham Kumar Singh

License
This project is licensed under the MIT License.

Copyright (c) 2026 Shubham Kumar Singh

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.



