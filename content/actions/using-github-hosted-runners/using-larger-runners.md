---
title: Using larger runners
shortTitle: Larger runners
intro: '{% data variables.product.prodname_dotcom %} offers larger runners with more RAM and CPU.'
product: '{% data reusables.gated-features.hosted-runners %}'
versions:
  feature: actions-hosted-runners
---

## Overview of {% data variables.actions.hosted_runner %}s

In addition to the [standard {% data variables.product.prodname_dotcom %}-hosted runners](/actions/using-github-hosted-runners/about-github-hosted-runners#supported-runners-and-hardware-resources), {% data variables.product.prodname_dotcom %} also offers customers on {% data variables.product.prodname_team %} and {% data variables.product.prodname_ghe_cloud %} plans a range of {% data variables.actions.hosted_runner %}s with more RAM and CPU. These runners are hosted by {% data variables.product.prodname_dotcom %} and have the runner application and other tools preinstalled.

When {% data variables.actions.hosted_runner %}s are enabled for your organization, a default runner group is automatically created for you with a set of four pre-configured {% data variables.actions.hosted_runner %}s.

When you add a {% data variables.actions.hosted_runner %} to an organization, you are defining a type of machine from a selection of available hardware specifications and operating system images. {% data variables.product.prodname_dotcom %} will then create multiple instances of this runner that scale up and down to match the job demands of your organization, based on the autoscaling limits you define.

## Machine specs for {% data variables.actions.hosted_runner %}s

| Size (vcpu) | Memory (RAM) | Storage (SSD) |
| ------------- | ------------- | ------------- |
| 4 cores | 16 GB | 150 GB|
| 8 cores | 32 GB | 300 GB |
| 16 cores | 64 GB | 600 GB |
| 32 cores | 128 GB | 1200 GB |
| 64 cores | 256 GB | 2040 GB |

### Additional features for {% data variables.actions.hosted_runner %}s

Compared to standard {% data variables.product.prodname_dotcom %}-hosted, {% data variables.actions.hosted_runner %}s have the following additional features:

- For Ubuntu runners, hardware acceleration for the Android SDK tools is enabled. This makes running Android tests much faster and consume fewer minutes. For more information on Android hardware acceleration, see the [Android Developer documentation](https://developer.android.com/studio/run/emulator-acceleration).

For a full list of included tools for each runner operating system, see the [{% data variables.product.prodname_actions %} Runner Images](https://github.com/actions/runner-images) repository.

## Architectural overview of {% data variables.actions.hosted_runner %}s

The {% data variables.actions.hosted_runner %}s are managed at the organization level, where they are arranged into groups that can contain multiple instances of the runner. They can also be created at the enterprise level and shared with organizations in the hierarchy. Once you've created a group, you can then add a runner to the group and update your workflows to target either the group name or the label assigned to the {% data variables.actions.hosted_runner %}. You can also control which repositories are permitted to send jobs to the group for processing. For more information about groups, see "[AUTOTITLE](/actions/using-github-hosted-runners/controlling-access-to-larger-runners)."

In the following diagram, a class of hosted runner named `ubuntu-20.04-16core` has been defined with customized hardware and operating system configuration.

![Diagram showing a larger runner being used by a workflow because of the runner's label.](/assets/images/help/actions/hosted-runner.png)

1. Instances of this runner are automatically created and added to a group called `grp-ubuntu-20.04-16core`.
2. The runners have been assigned the label `ubuntu-20.04-16core`.
3. Workflow jobs use the `ubuntu-20.04-16core` label in their `runs-on` key to indicate the type of runner they need to execute the job.
4. {% data variables.product.prodname_actions %} checks the runner group to see if your repository is authorized to send jobs to the runner.
5. The job runs on the next available instance of the `ubuntu-20.04-16core` runner.

## Autoscaling {% data variables.actions.hosted_runner %}s

Your {% data variables.actions.hosted_runner %}s can be configured to automatically scale to suit your needs. When jobs are submitted for processing, more machines can be automatically provisioned to run the jobs, until reaching a pre-defined maximum limit. Each machine only handles one job at a time, so these settings effectively determine the number of jobs that can be run concurrently.

During the runner deployment process, you can configure the _Max_ option, which allows you to control your costs by setting the maximum parallel number of machines that are created in this set. A higher value here can help avoid workflows being blocked due to parallelism.

## Networking for {% data variables.actions.hosted_runner %}s

By default, {% data variables.actions.hosted_runner %}s receive a dynamic IP address that changes for each job run. Optionally, {% data variables.product.prodname_ghe_cloud %} customers can configure their {% data variables.actions.hosted_runner %}s to receive a static IP address from {% data variables.product.prodname_dotcom %}'s IP address pool. When enabled, instances of the {% data variables.actions.hosted_runner %} will receive an address from a range that is unique to the runner, allowing you to use this range to configure a firewall allowlist. {% ifversion fpt %}You can use up to 10 static IP address ranges in total across all your {% data variables.actions.hosted_runner %}s{% endif %}{% ifversion ghec %}You can use up to 10 static IP address ranges for the {% data variables.actions.hosted_runner %}s created at the enterprise level. In addition, you can use up to 10 static IP address ranges for the {% data variables.actions.hosted_runner %}s created at the organization level, for each organization in your enterprise{% endif %}.

{% note %}

**Note**: If runners are unused for more than 30 days, their IP address ranges are automatically removed and cannot be recovered.

{% endnote %}

## Planning for {% data variables.actions.hosted_runner %}s

### Create a runner group

Runner groups are used to collect sets of virtual machines and create a security boundary around them. You can then decide which organizations or repositories are permitted to run jobs on those sets of machines. During the {% data variables.actions.hosted_runner %} deployment process, the runner can be added to an existing group, or otherwise it will join a default group. You can create a group by following the steps in "[AUTOTITLE](/actions/using-github-hosted-runners/controlling-access-to-larger-runners)."

### Understanding billing

{% note %}

**Note**: The {% data variables.actions.hosted_runner %}s do not use included entitlement minutes, and are not free for public repositories.

{% endnote %}

Compared to standard {% data variables.product.prodname_dotcom %}-hosted runners, {% data variables.actions.hosted_runner %}s are billed differently. For more information, see "[AUTOTITLE](/billing/managing-billing-for-github-actions/about-billing-for-github-actions#per-minute-rates)".

## Adding a {% data variables.actions.hosted_runner %} to an enterprise

You can add {% data variables.actions.hosted_runner %}s to an enterprise, where they can be assigned to multiple organizations. The organization admins can then control which repositories can use the runners. To add a {% data variables.actions.hosted_runner %} to an enterprise, you must be an enterprise owner.

{% data reusables.actions.add-hosted-runner-overview %}

{% data reusables.enterprise-accounts.access-enterprise %}
{% data reusables.enterprise-accounts.policies-tab %}
{% data reusables.enterprise-accounts.actions-tab %}
{% data reusables.enterprise-accounts.actions-runners-tab %}
{% data reusables.actions.add-hosted-runner %}
1. To allow organizations to access your {% data variables.actions.hosted_runner %}s, you specify the list of organizations that can use it. For more information, see "[Managing access to your runners](#managing-access-to-your-runners)."

## Adding a {% data variables.actions.hosted_runner %} to an organization

You can add a {% data variables.actions.hosted_runner %} to an organization, where the organization admins can control which repositories can use it.

{% data reusables.actions.add-hosted-runner-overview %}

{% data reusables.organizations.navigate-to-org %}
{% data reusables.organizations.org_settings %}
{% data reusables.organizations.settings-sidebar-actions-runners %}
{% data reusables.actions.add-hosted-runner %}
1. To allow repositories to access your {% data variables.actions.hosted_runner %}s, add them to the list of repositories that can use it. For more information, see "[Managing access to your runners](#managing-access-to-your-runners)."

## Running jobs on your runner

Once your runner type has been defined, you can update your workflow YAML files to send jobs to your newly created runner instances for processing. You can use runner groups or labels to define where your jobs run.

{% note %}

**Note:** When you add a {% data variables.actions.hosted_runner %}, it is automatically assigned default labels that correspond to the runner name and its operating system. You cannot add custom labels to {% data variables.actions.hosted_runner %}s, but you can use the default labels or the runner's group to send jobs to specific types of runners.

{% endnote %}

Only owner or administrator accounts can see the runner settings. Non-administrative users can contact the organization administrator to find out which runners are enabled. Your organization administrator can create new runners and runner groups, as well as configure permissions to specify which repositories can access a runner group.

### Using groups to control where jobs are run

{% data reusables.actions.jobs.example-runs-on-groups %}

### Using labels to control where jobs are run

In this example, a runner group is populated with Ubuntu 16-core runners, which have also been assigned the label `ubuntu-20.04-16core`. The `runs-on` key sends the job to any available runner with a matching label:

```yaml
name: learn-github-actions
on: [push]
jobs:
  check-bats-version:
    runs-on:
      labels: ubuntu-20.04-16core
    steps:
      - uses: {% data reusables.actions.action-checkout %}
      - uses: {% data reusables.actions.action-setup-node %}
        with:
          node-version: '14'
      - run: npm install -g bats
      - run: bats -v
```

### Using labels and groups to control where jobs are run

{% data reusables.actions.jobs.example-runs-on-labels-and-groups %}

{% data reusables.actions.section-using-unique-names-for-runner-groups %}

## Managing access to your runners

{% note %}

**Note**: Before your workflows can send jobs to {% data variables.actions.hosted_runner %}s, you must first configure permissions for the runner group. See the following sections for more information.

{% endnote %}

Runner groups are used to control which repositories can run jobs on your {% data variables.actions.hosted_runner %}s. You must grant access to the group from each level of the management hierarchy, depending on where you've defined the {% data variables.actions.hosted_runner %}:

- **Runners at the enterprise level**: Configure the runner group to grant access to all the required organizations. In addition, for each organization, you must configure the group to specify which repositories are allowed access.
- **Runners at the organization level**: Configure the runner group by specifying which repositories are allowed access.

For example, the following diagram has a runner group named `grp-ubuntu-20.04-16core` at the enterprise level. Before the repository named `octo-repo` can use the runners in the group, you must first configure the group at the enterprise level to allow access from the `octo-org` organization. You must then configure the group at the organization level to allow access from `octo-repo`.

![Diagram that shows a lock between a runner group at the enterprise level and an organization, and between the organization and two repositories owned by the organization.](/assets/images/help/actions/hosted-runner-mgmt.png)

### Allowing repositories to access a runner group

This procedure demonstrates how to configure group permissions at the enterprise and organization levels:

{% data reusables.actions.runner-groups-navigate-to-repo-org-enterprise %}
{% data reusables.actions.settings-sidebar-actions-runner-groups-selection %}
   - For runner groups in an enterprise: under **Organization access**, modify which organizations can access the runner group.
   - For runner groups in an organization: under **Repository access**, modify which repositories can access the runner group.

{% warning %}

**Warning**:

{% data reusables.actions.hosted-runner-security %}

For more information, see "[AUTOTITLE](/actions/using-github-hosted-runners/controlling-access-to-larger-runners)."

{% endwarning %}
