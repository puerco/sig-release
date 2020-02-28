# Start of release cycle (weeks 1-3)

## Week 1
While the Release Team Lead has always been a stakeholder in getting security fixes out the door, the Kubernetes security disclosures and response policy has evolved into something more formal. The documents linked below are required reading for an incoming Release Team Lead, who must understand and abide by the embargo policy.

| **Week** | **Activity** | **Teams Involved** |
| --- | --- | --- | 
| [ ] | Ensure the release team is fully filled, with members subscribed to the kubernetes-release-team and kubernetes-sig-release groups. |  |
| [ ] | Create an internal contact sheet and corresponding bit.ly link. Make sure to only give access to the kubernetes-release-team. Communicate with section leads to add their shadows so that you and your shadows can add them to the kubernetes-release-team. | Section Leads |
| [ ] | Ensure top-level `OWNERS_ALIASES` only includes Release Team personnel from four (4) releases, including the current one. |  |
| [ ] | Create and finalize the release schedule, blocking test gates, and role assignments as a pull request in: kubernetes/sig-release/releases/release-x.y/README.md Note: Do not ship the release on a Monday, to avoid preparing for the release on a weekend. Aim for Tuesday. |  |
| [ ] | Send an update to kubernetes-dev and kubernetes-sig-leads mailing list to announce the start of the release cycle, including any notable changes in the release process, key dates, and links to important documents |  |
| [ ] | Create the retrospective document and corresponding bit.ly link |  |
| [ ] | Begin meeting with SIGs to introduce yourself | SIGs |
| [ ] | Begin paying attention to CI signal, as it may begin degrading soon after the prior release is cut and any slips must be caught and rectified promptly. | CI signal |
| [ ] | Request, in coordination with CI Signal Lead, a representative from SIG-Scalability to give a weekly update on the release meeting notes. Prepare to have a release team representative attend SIG-scalability's meeting two or three times throughout the release. | CI signal |
| [ ] | Meet your Shadows and create a communication channel with them. Establish expectations and share out work - delegate! | Shadows |
| [ ] | Request review of this document by the Release Team Lead shadow(s). The shadow(s) should also take all actions in this document around joining groups and requesting access permissions. | Shadows |

## Week 2

| **Week** | **Activity** | **Teams Involved** |
| --- | --- | --- | 
| [ ] | Assist the Enhancements Lead in collecting planned work from SIGs | Enhancements |
| [ ] | Schedule weekly Release Team meetings on a day that is most acceptable to the team. These will eventually turn into burndown meetings and occur daily. Invite the kubernetes-sig-release group. | |
| [ ] | Poll Release Team membership and schedule a weekly alternate meeting to better enable more attendance outside of the Americas. | |
| [ ] | Add key event dates to the Kubernetes Release Calendar during the cycle. Ensure major calendar events are set to send an email reminder one week in advance. | |
| [ ] | Begin reporting release status at the community meeting | |
| [ ] | Continue meeting with SIGs for introductions | SIGs |

## Week 3
| **Week** | **Activity** | **Teams Involved** |
| --- | --- | --- | 
| [ ] | Create the release notes draft file in the release directory per the standard above | Release Notes |
| [ ] | Prepare for x.y.0-alpha.0 "release", specifically that there is a Branch Manager available to support the team, and that master-blocking tests are all green. The alpha.0 artifacts were created already as a part of the prior release. This synthetic notation is a point to review process with the Branch Manager. | Branch managers |
| [ ] | Identify any other dependent ecosystem projects that need release coordination | |
| [ ] | Announce/email that the following week is Enhancements Freeze and what that means | |


