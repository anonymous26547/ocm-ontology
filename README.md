## The Obligation Compliance Model
The Obligation Compliance Model (OCM) Ontology  provides a formal framework to model, represent, and reason about obligations in usage control and compliance scenarios. 
This ontology is expressed using OWL 2 and enhanced with SWRL rules to capture obligation execution logic.

## Project Structure

| File / Directory                  | Description |
|----------------------------------|-------------|
| `ontology-ocm.ttl`               | OWL 2 ontology defining the main concepts of the Obligation Compliance Model, including obligations, actions, temporal constraints, etc. |
| `rules.md`                        | Original SWRL rules describing the execution model for obligations (e.g., fulfillment, violation, expiration). |
| `ontology-ocm-with-swrl.ttl`      | Combined ontology file containing both the OWL 2 definitions and SWRL rules, enabling automated reasoning for obligation compliance. |
| `test-cases/`                     | Directory containing sample RDF data and queries to test and validate the ontology and rules. |
