# Open Grant Proposal: `Shaped Opal`

**Name of Project:** HLDB

**Proposal Category:** `devtools-libraries`

**Proposer:** [`@tabcat`](https://github.com/tabcat)

**(Optional) Technical Sponsor:** [`@autonome`](https://github.com/autonome)

**Do you agree to open source all work you do on behalf of this RFP and dual-license under MIT or APACHE2 licenses?:** Yes

# Project Description

<!-- Please describe exactly what you are planning to build. Make sure to include the following: -->

This grant builds on work from a preceeding grant titled [Rough Opal](https://github.com/tabcat/rough-opal). To summarize the work that grant dealt with:

- Write a protocol specification for `Opal`
- Implement that specification in Typescript
- Build a Zzzync replicator module
- Setup Network Simulation Tests with Testground

> During the that grant the protocol and implementation were renamed from Opal to HLDB (**H**yper**l**ocal **D**ata**b**ase) and Welo, respectively.

This grant will focus on making Welo, the HLDB implementation in Typescript, a bit faster.

What is now Welo was to be a rewrite of OrbitDB.
The rewrite had two primary goals.
The first goal was the ability stream the log in *ascending* order.
Streaming in *descending* order can be done by traversing Merkle-DAG links from disk, but links are one way in a directed graph.
The second goal was the ability to open the database without loading all entries into memory first.
This required traversing the entire log from head to tail which could take multiple seconds.
Both of these goals have been satisfied at a cost in performance; a result of increased IO operations.
  
Improving the current solutions in Welo will involve Write-Ahead Logging database commits, caching the database in memory, and swapping IPLD's HAMT ADL for a more scalable datastructure.
There's also a new element that will have a significant impact.
Replication between database peers involves sharing IPLD nodes and at the moment this is done using IPFS and Pubsub.
These two are a very powerful combination and work well.
There are a few other ways to sync DAGs besides bitswap; one I have wanted to try is from [ept/byzantine-eventual](https://github.com/ept/byzantine-eventual) (algorithm 2).
It involves using bloom-filters, which have been discussed by the IPFS community as a way to quickly syncing DAGs.

The Zzzync replicator module for Welo will also be improved.
Replicator uploads will be batched using the same Write-Ahead Log type technique as the database.

<!-- - Start with the need or problem you are trying to solve with this project. -->

<!-- - Describe why your solution is going to adequately solve this problem. -->

<!-- This section should be 2-3 paragraphs long. -->

## Value

<!-- Please describe in more detail why this proposal is valuable for the Filecoin ecosystem. Answer the following questions: -->
<!-- - What are the benefits to getting this right? -->
<!-- - What are the risks if you don't get it right? -->
<!-- - What are the risks that will make executing on this project difficult? -->

HLDB is being designed to work well with content-addressed storage like IPFS and Filecoin.
The idea is to use these systems to easily build applications that are dynamic and peer-to-peer but not fleeting.

The most difficult item in this grant will be implementing the new replication algorithm.
It is however, well explained in the paper and there are 2 reference implementations.

<!-- This section should be 1-3 paragraphs long. -->

## Deliverables

<!-- Please describe in details what your final deliverable for this project will be. Include a specification of the project and what functionality the software will deliver when it is finished. -->

- [ ] Similar benchmark performance compared with OrbitDB
- [ ] Improved replication benchmark performance compared with OrbitDB
- [ ] BEC Replicator included with default bundle
- [ ] Zzzync Replicator batches uploads
- [ ] End Shaped Opal with >95% test coverage

## Development Roadmap

<!-- Please break up your development work into a clear set of milestones. This section needs to be very detailed (will vary on the project, but aim for around 2 pages for this section). -->

- [ ] Write-Ahead Log (1 month)
  - [ ] Identify Batch-able IO Operations
  - [ ] Replica Graph Mutations are commited to a Write-Ahead Log
  - [ ] Replica Graph Mutations are applied to an In-Memory Adjacency List
  - [ ] Replica Graph Mutations are commited to an On-Disk Adjacency List
  - [ ] Replica Graph Mutations are uncommited from a Write-Ahead Log
  - [ ] Replica Graph Mutations can be unapplied from an In-Memory Adjacency List

- [ ] Database Caching (2 weeks)
  - [ ] Database Entries are Cached
  - [ ] Database Identities are Cached
  - [ ] Database Replica is Cached
  - [ ] Database Index is Cached

- [ ] Zzzync Replicator Batches Uploads (2 weeks)

- [ ] Implement BEC Replication Algorithm (1 month)
  - [ ] Design BEC Replicator Implementation
  - [ ] Create protocol-id for BEC Replicator
  - [ ] Add BEC Replicator Libp2p Protocol Handler and Match Functions
  - [ ] Connect BEC Replicator Protocol Handler to Replica
  - [ ] Add Tests for BEC Replicator
  - [ ] Add BEC Replicator to Default Welo Bundle

<!-- For each milestone, please describe: -->
<!-- - The software functionality that we can expect after the completion of each milestone. This should be detailed enough that it can be used to ensure that the software meets the specification you outlined in the Deliverables. -->
<!-- - How many people will be working on each milestone and their roles -->
<!-- - The amount of funding required for each milestone -->
<!-- - How much time this milestone will take to achieve (using real dates) -->

## Total Budget Requested

<!--Sum up the total requested budget across all milestones, and include that figure here. Also, please include a budget breakdown to specify how you are planning to spend these funds. -->

| Budget | Duration | Payable As |
| --- | --- | --- |
| $21600 | MAR-MAY 2023 | FILECOIN |

1 Full-time Engineer over 3 months at 45$/hr

Payments preferred in Filecoin; on a monthly basis or as milestones are completed.

## Maintenance and Upgrade Plans

<!-- Specify your team's long-term plans to maintain this software and upgrade it over time. -->

The long-term goal of the project is to provide a robust way to collaborate using content addressed data.
The currently method chosen to do this involve G-Sets + Merkle-CRDTs with a Strong Eventual Consistency model.
While the method may change, the category of applications this project targets will not.

The last grant included work that made the project easier to maintain overtime.
Things like automatic publishing, change-logs, API docs, and testing were included.
These are all becoming standard for larger open source projects.
During the last grant I also made [`copy-deps`](https://github.com/tabcat/copy-deps).
It helps Welo keep it's dependencies equal with `ipfs-core`.

After the work laid out in this grant is finished, dynamic access-control will be the next dragon to slay.
Dynamic access-control involes not just controlling writes through digital signatures, but also reads through encryption.
There is not a complete solution for dynamic access-control in this context.
Currently it is possible to add and remove access; but not without affecting the database's entire history.
[Decentralized multi-writer encryption](https://martin.kleppmann.com/2021/11/17/decentralized-key-agreement.html) schemes and other examples of capability-based security are good areas to start research for this problem.

# Team

## Team Members

<!-- - Team Member 1 -->
Daniel, [@tabcat](https://github.com/tabcat)

<!-- - Team Member 2 -->
<!-- - Team Member 3 -->
<!-- - ... -->

<!-- ## Team Member LinkedIn Profiles -->

<!-- - Team Member 1 LinkedIn profile -->
<!-- - Team Member 2 LinkedIn profile -->
<!-- - Team Member 3 LinkedIn profile -->
<!-- - ... -->

## Team Website

https://github.com/hldb

https://github.com/cypsela

<!-- Please link to your team's website here (make sure it's `https`) -->

## Relevant Experience

<!-- Please describe (in words) your team's relevant experience, and why you think you are the right team to build this project. You can cite your team's prior experience in similar domains, doing similar dev work, individual team members' backgrounds, etc. -->

This work builds on a previously accepted grant, (Rough Opal)[https://github.com/tabcat/rough-opal], by the same team, which delivered a [specification](https://github.com/hldb/specs) and [Typescript implementation](https://github.com/hldb/welo) of the HLDB protocol (both previously named Opal).

## Team code repositories

<!-- Please provide links to your team's prior code repos for similar or related projects. -->

- [specs](https://github.com/hldb/specs): HLDB protocol specifications
- [welo](https://github.com/hldb/welo): HLDB implementation in Typescript
- [zzzync](https://github.com/tabcat/zzzync): experimental replicator for welo. originally a 2 day hack using web3.storage, first prize at hackfs2022 [[ref1]](https://ethglobal.com/showcase/zzzync-xk96u)
- [sailplane](https://github.com/cypsela/sailplane-web): collaborative filesystem web app built with orbitdb and ipfs, finalist at hackfs2020 [[ref1]](https://showcase.ethglobal.com/hackfs/sailplane-web) [[ref2]](https://filecoin.io/blog/posts/meet-the-hackfs-teams-vol.3/)
- [orbitdb](https://github.com/orbitdb): community contributer and former full-time maintainer

# Additional Information
<!-- How did you learn about the Open Grants Program? -->
<!-- Please provide the best email address for discussing the grant agreement and general next steps. -->
email: `tabcat00@proton.me`
<!-- Please include any additional information that you think would be useful in helping us to evaluate your proposal. -->

---

#### If you have questions or want more specific information please contact me or leave a comment
