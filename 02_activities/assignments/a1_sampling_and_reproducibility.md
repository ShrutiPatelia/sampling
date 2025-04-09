# ASSIGNMENT: Sampling and Reproducibility in Python

Read the blog post [Contact tracing can give a biased sample of COVID-19 cases](https://andrewwhitby.com/2020/11/24/contact-tracing-biased/) by Andrew Whitby to understand the context and motivation behind the simulation model we will be examining.

Examine the code in `whitby_covid_tracing.py`. Identify all stages at which sampling is occurring in the model. Describe in words the sampling procedure, referencing the functions used, sample size, sampling frame, any underlying distributions involved, and how these relate to the procedure outlined in the blog post.

Run the Python script file called whitby_covid_tracing.py as is and compare the results to the graphs in the original blog post. Does this code appear to reproduce the graphs from the original blog post?

Modify the number of repetitions in the simulation to 100 (from the original 1000). Run the script multiple times and observe the outputted graphs. Comment on the reproducibility of the results.

Alter the code so that it is reproducible. Describe the changes you made to the code and how they affected the reproducibility of the script file. The output does not need to match Whitby’s original blogpost/graphs, it just needs to produce the same output when run multiple times

# Author: Shruti Patelia

```
Identify all stages of sampling:

There seems to be 3 stages where sampling is occuring. 
1) STAGE ONE - Initial Infected selection based on event attendance:
    
    Sampling procedure: Simple random sampling because everyone has an equal chance of being infected. 
    
    Sample size: 1000 people (200 at weddings; 800 at brunches)
    
    Sampling frame: The number of people (1000) attending the events in a single time period.
    
    Functions used: infected_indices = np.random.choice(ppl.index, size=int(len(ppl) * ATTACK_RATE), replace=False)

    Underlying distribution: Uniform distribution. This means that every individual in the population (1000) has an equal chance of being infected. This is the same procedure as the Whitby post which suggested "assume that infection is randomly distributed among attendees..." and that "infections are not more likely to occur at weddings than brunches." 

2) STAGE 2 - Primary Contact Tracing:
    Sampling procedure: Simple random sampling because everyone has an equal chance of being infected.

    Sample size: 10% of the population 

    Sampling frame: Only the infected undividuals (100 people; 10%). Each individual has a 20% chance of being successfully traced

    Functions used: ppl.loc[ppl['infected'], 'traced'] = np.random.rand(sum(ppl['infected'])) < TRACE_SUCCESS

    Underlying distribution: binomial distribution. This means that each infected person is independently traced with a fixed probability (20%). This is indicated in the Whitby blog post as " only a portion of cases are traced."

3) STAGE 3 - Secondary tracing: 
    Sampling procedure: Deterministic/event-based - non probabilistic method. 

    Sample size: Varies - Dependent on the threshold of having ≥ 2 people traced individuals from primary tracing. If ≥ 2 people from an event are traced in primary tracing, then all infected attendees from that event are secondarily traced

    Sampling frame: All infected individuals attending those specific event types (brunch and wedding) who were present with ≥ 2 people traced cases (infected individuals who are part of the event). 

    Functions used: event_trace_counts = ppl[ppl['traced'] == True]['event'].value_counts()
    events_traced = event_trace_counts[event_trace_counts >= SECONDARY_TRACE_THRESHOLD].index
    ppl.loc[ppl['event'].isin(events_traced) & ppl['infected'], 'traced'] = True

    Underlying distribution: Indirectly influenced by the binomial distribution in the primary tracing stage. This is similar to the Whiby blog post where "if a single event is traced, all other infected attendees of that event are also traced." This particular stage may introduce some bias because of the disproportionate sample in weddings (smaller sample and more likely to meet the threshold) compared to brunches.

COMPARING GRAPHS:
    The histogram produced from the Whiby code (infections vs. traces) seems to reproduce a similar histogram, but not exactly like the blog post. The blue bars (actual infections) are spread more evenly and the red bars (traced infections) are skewed towards the wedding. As a result, the code illustrates the bias described in the blog post that contact tracing is biased towards certain events like weddings as infection sources. 

MAKING THE CODE REPRODUCIBLE:

1) Setting a fixed random seed
    import random
    np.random.seed(123)
    random.seed(123)

    This change ensures the random number generation is consistent across runs, so the random processes (infected selection, tracing success) are reproducible

2) Run the simulation 100 times
    results = [simulate_event(m) for m in range(100)] 

    This change allows for quicker similation than 1000. 













```


## Criteria

|Criteria|Complete|Incomplete|
|--------|----|----|
|Altercation of the code|The code changes made, made it reproducible.|The code is still not reproducible.|
|Description of changes|The author explained the reasonings for the changes made well.|The author did not explain the reasonings for the changes made well.|

## Submission Information

🚨 **Please review our [Assignment Submission Guide](https://github.com/UofT-DSI/onboarding/blob/main/onboarding_documents/submissions.md)** 🚨 for detailed instructions on how to format, branch, and submit your work. Following these guidelines is crucial for your submissions to be evaluated correctly.

### Submission Parameters:
* Submission Due Date: `23:59 - 09/04/2025`
* The branch name for your repo should be: `assignment-1`
* What to submit for this assignment:
    * This markdown file (a1_sampling_and_reproducibility.md) should be populated.
    * The `whitby_covid_tracing.py` should be changed.
* What the pull request link should look like for this assignment: `https://github.com/<your_github_username>/sampling/pull/<pr_id>`
    * Open a private window in your browser. Copy and paste the link to your pull request into the address bar. Make sure you can see your pull request properly. This helps the technical facilitator and learning support staff review your submission easily.

Checklist:
- [ ] Create a branch called `assignment-1`.
- [ ] Ensure that the repository is public.
- [ ] Review [the PR description guidelines](https://github.com/UofT-DSI/onboarding/blob/main/onboarding_documents/submissions.md#guidelines-for-pull-request-descriptions) and adhere to them.
- [ ] Verify that the link is accessible in a private browser window.

If you encounter any difficulties or have questions, please don't hesitate to reach out to our team via the help channel in Slack. Our Technical Facilitators and Learning Support staff are here to help you navigate any challenges.
