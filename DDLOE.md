# Distributed Deep Learning In Open Environments


### Preliminary

Currently, the size of deep learning model growth rate is doubling every 3.4 months, while consumer hardware grows much slower, by doubling every two years, most of the progress currently being done is by buying more physical hardware. Currently the biggest model is Microsoft’s Turing-NLG at 17 billion parameters, which had to use 256 GPUs, the second biggest is Nvdia’s MegatronLM at 8.3 billion parameters, and trained on 1024 GPUs.

So the good news its still possible to have better models with less hardware, if good optimization is done with better libraries, the overall trend is still buying more hardware, which means if the trend continues, only well funded labs can conduct cutting edge research on deep learning.

Google Research has came up with federated learning, which shows how to build big models without relying on a single big hardware box, although Google’s intention was to find a way to train models without having a single data repository, not performance. The textbook example for federated learning is Android’s keyboard, where a model can be trained on distrusted  fashion on each device locally, then sent to the main server.

The main concept behind federated learning can be refactored to help solve the first problem of ever increasing hardware costs, in use cases that don’t require privacy protection.


### Problem To Solve

The benchmark for computer vision is ImageNet, and there is a group of students who want to test their new model on the dataset. The issue is none of them have enough money to purchase AWS credit, but, from the 10 students, 3 have gaming desktops, and 7 have average macbooks.
They have decided that one person would start the initial random state for the model, and share it with the rest, then each person would train on the subset of the dataset and publish the gradient in json file with following format.

```
image: img1.jpeg
gradient:0.2425245

image: img2.jpeg
gradient: 0.5463353

image: img3.jpeg
gradient: 0.93752
```

Then the total average of the gradients can be used to build the end model, as if trained on all the previous images. This is how many large models are trained actually, with many servers distributing the workload and then averaging out the results.
The issue is, this can only work within a fully trusted environment, such as students working on the same project, or a datacenter owned by the same party.

If somehow outsiders were allowed to participate, in a similar way to Wikipedia or open source software, there is no way to weed out bad actors who would submit fake results to distort the function of the model. The way to check for the integrity of a given gradient, would be to backpropagate again to see if the same result equals to the submitted gradient.

With this problem, it becomes hard to scale outside trusted setups, and community projects online cannot exist, such as a large distributed deep learning grid to build large and open source models.

While there are about a dozen crypto-currency projects out there claiming to solve this exact issue, introducing money would only create new incentives to game the system.


### Proposal
Given the problem stated above, trust setups cannot be fully eliminated, but a reputation system can be structured to make sure bad actors cannot distort the model without investing a huge amount of time and resources. In other words, make reputation hard to gain, easy to lose.

Given a reputation with arbitrary values, for example the lowest being 0.001 while the highest is 0.999. Each participant is assigned the lowest reputation level once they join the network. And reputation grows up with one level with each file submitted, so each gradient uploaded should increase reputation by 0.001+.  So now the traing log files should look something similar to the following:

```
image: img1.jpeg
gradient:0.2425245
userid: 1
reputation: 0.134

image: img2.jpeg
gradient: 0.5463353
userid: 2
reputation: 0.009

image: img3.jpeg
gradient: 0.93752
userid: 3
reputation: 0.403

image: img4.jpeg
gradient: 0.42593
userid: 1
reputation: 0.134
```

The difference here, is before averaging the gradients, each gradient is multiplied by its reputation. So that user 1, would have their gradients only by about 13%, while user 3 has his gradient weight in by 40%, and user 2 would have a fraction of the weights. So everyone would have their gradients included, but the weights of their gradients are relative to the size of their reputation.

This way, bad actors can still help training the model, but would have to climb up hill to have their reputation go up and would have to wait and invest a lot to start effecting the end result of the model.

At the same time, other users can check the training log themselves and see if anyone is acting malicious, if someone finds a false gradient, they can request a fraud detection and submit the file name with the gradient, and the alleged false gradient. A trusted node can check the integrity of the claim, and if the statement is true, the user losses the reputation back to 0.001 and all the files are deleted from the training log, while the user who submitted the claim, is rewarded with half of the reputation of the bad actor. But if the claim is false, the user who submitted the claim is punished by setting their reputation to 0.001 as well, and deleting all the files from the training log.

As a result of this, honest user Bob, can participate in the network without the need to be trusted, first starts with a low reputation and near zero effect on the model, and overtime, as his reputation grows, his older gradients suddenly become more effective and have higher weights to the model.

While bad actor Mallory, would also participate in the network submitting false gradient results but with near zero effect, and overtime with her reputation growing, her chance of being caught by other users probing her gradients increases as well. If she submits 100 gradient results, and all are false, that would make it highly likely to be detected, but if she decided to submit a mix of legit and false gradients, that gives her a lower chance to be detected indeed, but also means her contribution is a mixture of honesty and malice.

With this setup, users probing for false gradients would start nitpicking those with high reputation more than users who just recently joined, while users with medium reputation would be in some middle ground.

So now even if a bad actor decides to submit 90% legitimate gradients 10% false ones, eventually once that users reaches a high level of reputation, someone would have caught him with one gradient at least, and kicked out of the network.



---

## Random notes.


- Reputation system doesn’t need to grow linear, it could be exponential, for example starting from the smallest float of 0.00000001 and then grows exponentially.

- The trusted node that judges false claims could be anyone the community decides, for example the project maintainer, or a group of users with high reputation who now have gained enough trust to be trusted nodes.
