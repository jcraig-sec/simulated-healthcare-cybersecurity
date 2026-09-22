# IoMT Micro-segmentation Architecture Plan

## Project Overview

This case study presents a Zero Trust micro-segmentation architecture for a simulated healthcare organization whose existing VLANs were documented but not enforced through access controls. The design isolates clinical systems, Internet of Medical Things devices, and facilities operational technology within Microsoft Azure.

The project included a subnet and security-group blueprint, an access-control rule ledger, and a 12-month budget reconciliation.

## Security Problem

The simulated environment allowed unrestricted communication between clinical, IoMT, and facilities systems. A compromised medical device or operational-technology system could potentially be used to move laterally toward critical electronic health record resources.

The proposed design replaces this implicit trust with explicitly authorized communication between security zones.

## Architecture

| Security Zone           | IP Range        | Protected Systems                                                               | Application Security Groups |
| ----------------------- | --------------- | ------------------------------------------------------------------------------- | --------------------------- |
| Zone A – Clinical Core  | `10.50.0.0/24`  | EHR database cluster and EHR API                                                | `ASG-EHR`, `ASG-EHR-API`    |
| Zone B – IoMT Perimeter | `10.10.0.0/16`  | 450 Alaris pumps, 120 ECG carts, 300 vital monitors, and 85 dispensing cabinets | Device-specific IoMT ASGs   |
| Zone C – Facilities OT  | `10.100.0.0/24` | HVAC sensors and building-management systems                                    | `ASG-Facilities-HVAC`       |

Separate Network Security Groups were assigned to each zone:

* `NSG-Zone-A`
* `NSG-Zone-B`
* `NSG-Zone-C`

## Access-Control Strategy

The proposed rule set was designed to:

* Block communication between the IoMT and Facilities OT zones
* Prevent Facilities OT systems from accessing EHR resources
* Restrict IoMT access to the Clinical Core
* Permit Alaris infusion pumps to reach the EHR API through TCP port 443
* Deny other unauthorized pump and IoMT traffic to the EHR API
* Use Azure Firewall for approved external and 5G edge communication
* Log permitted and denied traffic for monitoring and review

## Default-Deny Refinement

Instructor review identified that the original rule ledger did not fully override Azure’s default `AllowVNetInBound` and `AllowVNetOutBound` behavior across every security zone.

A corrected implementation would add explicit deny-all inbound and outbound rules at lower precedence than approved exceptions but higher precedence than Azure’s default rules. Each required IoMT-to-EHR flow would then be documented as an individual allow rule based on its source, destination, port, protocol, and operational purpose.

The revised rule order would follow this model:

1. Permit specifically approved clinical communication.
2. Permit required monitoring, logging, DNS, and update services.
3. Permit authorized 5G edge traffic through Azure Firewall.
4. Deny all remaining inter-zone inbound and outbound traffic.
5. Log denied traffic for investigation and policy tuning.

## 5G Edge Integration

The original memorandum referenced approved 5G edge communication but did not include it in the network blueprint or ACL ledger. A revised design would document the 5G edge as a separate controlled network path routed through Azure Firewall.

No 5G-connected device would receive direct access to the Clinical Core. Approved traffic would require an explicit firewall rule and would be inspected, logged, and limited to its required destination and service.

## Budget Reconciliation

| Security Control                                 |  Annual Cost |
| ------------------------------------------------ | -----------: |
| Azure Firewall and traffic processing            |      $12,000 |
| Microsoft Defender for IoT for 955 devices       |       $9,741 |
| Azure Monitor and log retention                  |      $18,000 |
| Network bandwidth and data transfer              |       $6,000 |
| Professional implementation and cutover services |      $60,000 |
| Azure NSGs and ASGs                              |           $0 |
| **Total Estimated Cost**                         | **$105,741** |

The proposed architecture remained **$74,259 below** the simulated organization’s $180,000 annual security budget.

## Skills Demonstrated

* Zero Trust architecture
* Network micro-segmentation
* Microsoft Azure NSG and ASG design
* Access-control rule development
* IoMT and operational-technology security
* Stateful traffic filtering
* Security logging and monitoring planning
* Cost estimation and budget reconciliation
* Technical memorandum development

## Lessons Learned

This project demonstrated that defining subnets or VLANs does not create meaningful isolation by itself. Effective segmentation requires explicit allow rules, enforceable deny rules, documented traffic dependencies, and continuous monitoring.

The architectural review also reinforced the importance of including every network path—such as external 5G connectivity—in both the technical blueprint and the access-control ledger.

## Disclaimer

This project is an educational case study based on a simulated healthcare environment. It contains no real patient information, production configurations, or proprietary organizational data.
