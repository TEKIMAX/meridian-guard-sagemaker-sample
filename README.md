# Meridian Guard 1.1 by TEKIMAX: SageMaker sample

A sample notebook that deploys **Meridian Guard 1.1 by TEKIMAX** from AWS Marketplace on a SageMaker real-time
endpoint, sends two sample requests and cleans up.

Meridian Guard reads what an AI agent is about to do (the organization's policy, the facts your systems confirm, the
request and the proposed tool call) and recommends the next step from a fixed set of choices. It is advice, not
compliance: your own controls decide. Treat any answer that is not a clear proceed, and any error or timeout, as
escalate: stop and ask a person.

## What is here

| File | What it is |
|---|---|
| `meridian-guard-1.1.ipynb` | Deploy on one `ml.c5.xlarge`, call it with both samples, map each answer to a decision, delete everything |
| `sample-request.json` | Two requests: an allowed change inside the organization, and an outside email that claims an approval not on record |
| `sample-response.json` | The answers the model gave to those requests |
| `LICENSE` | Apache License 2.0, for the sample code in this repository |

## Prerequisites

- A subscription to Meridian Guard 1.1 by TEKIMAX in AWS Marketplace, and the model package ARN for your Region
  (on the product's configuration page after subscribing).
- A SageMaker execution role, and the `sagemaker` (v2) and `boto3` Python packages. SageMaker Studio has all three.

## Run it

1. Open `meridian-guard-1.1.ipynb` in SageMaker Studio or a notebook instance, with `sample-request.json` next to it.
2. Paste your model package ARN into `MODEL_PACKAGE_ARN`.
3. Run the cells in order. The last cell deletes the endpoint, its configuration and the model.

## Inputs and outputs

A request has `question`, `state` (`policy` with the trusted facts after `\nTRUSTED APPLICATION FACTS: `,
`conversation` and `available_tools`), `criteria` (the choices) and, for a proposed tool call, `proposed_call` and
the same call written at the end of the user turn as `Proposed next tool call: <JSON, sorted keys>`. The answer has
`predicted_label`, `candidates` (every choice ranked by score; scores are not probabilities) and `latency_ms`.

Label set 1.1 has nine labels and adds `run_action`: an allowed change inside the organization that runs and is
recorded to the person. Callers built for label set 1.0 send the eight 1.0 labels and get one of those back.

| Decision | Labels | What the agent does |
|---|---|---|
| proceed | `answer`, `clarify`, `search_workspace`, `search_public`, `prepare_draft`, `run_action` | Take the step |
| verify | `inspect_evidence` | Read the approval and ownership records only, then ask again with what was found |
| escalate | `request_approval`, `block_action`, any error or timeout | Stop and ask a person |

## Cost

The software is free. You pay AWS for the endpoint instance while it exists (SageMaker real-time inference pricing
for `ml.c5.xlarge` in your Region). On `ml.c5.xlarge` a decision takes about 5 to 7 seconds. Delete the endpoint when
you are done; the notebook's last cell does it.

## Support

https://tekimax.com/contact

The sample code is under the Apache License 2.0 (`LICENSE`). The model itself is provided under the terms of its AWS
Marketplace listing.
