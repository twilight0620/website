---
title: 已完成 Job 的自动清理
content_type: concept
weight: 70
---
<!--
title: Automatic Clean-up for Finished Jobs
content_type: concept
weight: 70
-->

<!-- overview -->

{{< feature-state for_k8s_version="v1.23" state="stable" >}}

<!--
TTL-after-finished {{<glossary_tooltip text="controller" term_id="controller">}} provides a 
TTL (time to live) mechanism to limit the lifetime of resource objects that 
have finished execution. TTL controller only handles 
{{< glossary_tooltip text="Jobs" term_id="job" >}}.
-->
TTL-after-finished {{<glossary_tooltip text="控制器" term_id="controller">}} 提供了一种 TTL 机制来限制已完成执行的资源对象的生命周期。
TTL 控制器目前只处理 {{< glossary_tooltip text="Job" term_id="job" >}}。


<!-- body -->

<!--
## TTL-after-finished Controller

The TTL-after-finished controller is only supported for Jobs. A cluster operator can use this feature to clean
up finished Jobs (either `Complete` or `Failed`) automatically by specifying the
`.spec.ttlSecondsAfterFinished` field of a Job, as in this
[example](/docs/concepts/workloads/controllers/job/#clean-up-finished-jobs-automatically).
-->
## TTL-after-finished 控制器

TTL-after-finished 控制器只支持 Job。集群操作员可以通过指定 Job 的 `.spec.ttlSecondsAfterFinished`
字段来自动清理已结束的作业（`Complete` 或 `Failed`），如
[示例](/zh/docs/concepts/workloads/controllers/job/#clean-up-finished-jobs-automatically)
所示。

<!--
The TTL-after-finished controller will assume that a job is eligible to be cleaned up
TTL seconds after the job has finished, in other words, when the TTL has expired. When the
TTL-after-finished controller cleans up a job, it will delete it cascadingly, that is to say it will delete
its dependent objects together with it. Note that when the job is deleted,
its lifecycle guarantees, such as finalizers, will be honored.
-->
TTL-after-finished 控制器假设作业能在执行完成后的 TTL 秒内被清理，也就是当 TTL 过期后。
当 TTL 控制器清理作业时，它将做级联删除操作，即删除资源对象的同时也删除其依赖对象。
注意，当资源被删除时，由该资源的生命周期保证其终结器（Finalizers）等被执行。

<!--
The TTL seconds can be set at any time. Here are some examples for setting the
`.spec.ttlSecondsAfterFinished` field of a Job:
-->
可以随时设置 TTL 秒。以下是设置 Job 的 `.spec.ttlSecondsAfterFinished` 字段的一些示例：

<!--
* Specify this field in the job manifest, so that a Job can be cleaned up
  automatically some time after it finishes.
* Set this field of existing, already finished jobs, to adopt this new feature.
* Use a
  [mutating admission webhook](/docs/reference/access-authn-authz/extensible-admission-controllers/#admission-webhooks)
  to set this field dynamically at job creation time. Cluster administrators can
  use this to enforce a TTL policy for finished jobs.
* Use a
  [mutating admission webhook](/docs/reference/access-authn-authz/extensible-admission-controllers/#admission-webhooks)
  to set this field dynamically after the job has finished, and choose
  different TTL values based on job status, labels, etc.
-->
* 在作业清单（manifest）中指定此字段，以便 Job 在完成后的某个时间被自动清除。
* 将此字段设置为现有的、已完成的作业，以采用此新功能。
* 在创建作业时使用 [mutating admission webhook](/zh/docs/reference/access-authn-authz/extensible-admission-controllers/#admission-webhooks)
  动态设置该字段。集群管理员可以使用它对完成的作业强制执行 TTL 策略。
* 使用 [mutating admission webhook](/zh/docs/reference/access-authn-authz/extensible-admission-controllers/#admission-webhooks)
  在作业完成后动态设置该字段，并根据作业状态、标签等选择不同的 TTL 值。

<!--
## Caveat

### Updating TTL Seconds

Note that the TTL period, e.g. `.spec.ttlSecondsAfterFinished` field of Jobs,
can be modified after the job is created or has finished. However, once the
Job becomes eligible to be deleted (when the TTL has expired), the system won't
guarantee that the Jobs will be kept, even if an update to extend the TTL
returns a successful API response.
-->
## 警告

### 更新 TTL 秒数

请注意，在创建 Job 或已经执行结束后，仍可以修改其 TTL 周期，例如 Job 的
`.spec.ttlSecondsAfterFinished` 字段。
但是一旦 Job 变为可被删除状态（当其 TTL 已过期时），即使您通过 API 增加其 TTL
时长得到了成功的响应，系统也不保证 Job 将被保留。

<!--
### Time Skew

Because TTL-after-finished controller uses timestamps stored in the Kubernetes resources to
determine whether the TTL has expired or not, this feature is sensitive to time
skew in the cluster, which may cause TTL-after-finished controller to clean up resource objects
at the wrong time.
-->
### 时间偏差  {#time-skew}

由于 TTL-after-finished 控制器使用存储在 Kubernetes 资源中的时间戳来确定 TTL 是否已过期，
因此该功能对集群中的时间偏差很敏感，这可能导致 TTL-after-finished 控制器在错误的时间清理资源对象。

<!--
Clocks aren't always correct, but the difference should be
very small. Please be aware of this risk when setting a non-zero TTL.
-->
时钟并不总是如此正确，但差异应该很小。
设置非零 TTL 时请注意避免这种风险。

## {{% heading "whatsnext" %}}

<!--
* [Clean up Jobs automatically](/docs/concepts/workloads/controllers/jobs-run-to-completion/#clean-up-finished-jobs-automatically)
* [Design doc](https://github.com/kubernetes/enhancements/blob/master/keps/sig-apps/592-ttl-after-finish/README.md)
-->
* [自动清理 Job](/zh/docs/concepts/workloads/controllers/job/#clean-up-finished-jobs-automatically)
* [设计文档](https://github.com/kubernetes/enhancements/blob/master/keps/sig-apps/592-ttl-after-finish/README.md)

