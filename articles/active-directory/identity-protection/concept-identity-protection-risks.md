---
title: 什么是风险？ Azure AD 标识保护
description: 介绍 Azure AD Identity Protection 中的风险
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 08/15/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c92994fee6de4c56257343af2ef418393b505ad
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88507427"
---
# <a name="what-is-risk"></a>什么是风险？

Azure AD Identity Protection 中的风险检测包括任何与目录中的用户帐户相关的已识别的可疑操作。

标识保护为组织提供了强大资源的访问权限，可快速查看和响应这些可疑操作。 

![显示风险用户和登录的安全性概述](./media/concept-identity-protection-risks/identity-protection-security-overview.png)

## <a name="risk-types-and-detection"></a>风险类型和检测

存在两种类型的风险 **用户** 和 **登录** 以及两种类型的检测或计算： **实时** 和 **脱机**。

### <a name="user-risk"></a>用户风险

用户风险表示给定标识或帐户泄露的可能性。 

这些风险是使用 Microsoft 的内部和外部威胁情报源进行脱机计算的，其中包括安全研究人员、执法人员、Microsoft 安全团队以及其他受信任的来源。

| 风险检测 | 说明 |
| --- | --- |
| 凭据泄露 | 此风险检测类型表示用户的有效凭据已泄漏。 当网络犯罪分子泄露合法用户的有效密码时，他们通常会共享这些凭据。 通常，这种共享方式是通过公开发布到暗 web、粘贴站点或通过贸易并在黑色市场上销售凭据来完成的。 当 Microsoft 泄漏的凭据服务从深色 web、粘贴站点或其他源获取用户凭据时，将根据 Azure AD 用户的当前有效凭据来检查它们以查找有效匹配项。 有关泄漏的凭据的详细信息，请参阅 [常见问题](#common-questions)。 |
| Azure AD 威胁智能 | 此风险检测类型表示对于给定用户异常的用户活动，或者与基于 Microsoft 内部和外部威胁智能源的已知攻击模式一致。 |

### <a name="sign-in-risk"></a>登录风险

登录风险表示给定的身份验证请求未由标识所有者授权的概率。 

这些风险可以使用 Microsoft 的内部和外部威胁智能源进行实时计算，或者使用 Microsoft 的内部和外部威胁智能源进行脱机计算，包括安全研究人员、执法人员、Microsoft 安全团队以及其他受信任的来源。

| 风险检测 | 检测类型 | 说明 |
| --- | --- | --- |
| 匿名 IP 地址 | 实时 | 此风险检测类型表示从匿名 IP 地址登录 (例如，Tor browser 或 anonymous VPN) 。 这些 IP 地址通常由希望隐藏其登录遥测数据（IP 地址、位置、设备等）的参与者使用以实现潜在恶意目的。 |
| 异常位置登录 | Offline | 此风险检测类型可标识从相距遥远的地理位置进行的两次登录，根据用户以往的行为，其中至少有一个位置属于异常。 除了若干其他因素外，此机器学习算法还考虑两次登录之间相隔的时间以及用户从第一个位置前往第二个位置所需的时间，因为这指示有不同的用户在使用相同的凭据。 <br><br> 此算法会忽略明显的“误报”，从而改善不可能前往条件，例如组织中其他用户定期使用的 VPN 和位置。 系统的初始学习期最早为14天或10个登录名，在此期间，它将学习新用户的登录行为。 |
| 受恶意软件感染的 IP 地址 | Offline | 此风险检测类型指示从感染了恶意软件的 IP 地址（即主动与僵尸服务器通信）登录。 此检测通过将用户设备的 IP 地址与在机器人服务器处于活动状态时与机器人服务器联系的 IP 地址进行关联来确定。 |
| 不熟悉的登录属性 | 实时 | 此风险检测类型考虑过去的登录历史记录 (IP、纬度/经度和 ASN) 查找异常登录。系统存储有关用户以前使用的位置的信息，并将其视为 "熟悉" 位置。 当从尚未在熟悉位置列表中列出的位置登录时，将触发此风险检测。 新创建的用户在一段时间内将处于 "学习模式"，在这种情况下，在我们的算法了解到用户的行为时，将会关闭风险检测。 学习模式持续时间是动态的，具体取决于算法收集有关用户登录模式的足够信息所花费的时间。 最短持续时间为五天。 用户可以在长时间处于非活动状态后返回到学习模式。 系统还会忽略从常用设备和接近熟悉位置的地理位置进行登录。 <br><br> 我们还对基本身份验证（或旧版协议）运行此检测。 由于这些协议没有新型属性（如客户端 ID），因此用来减少误报的遥测数据较为有限。 建议客户采用新式身份验证。 |
| 管理员确认用户遭入侵 | Offline | 此检测表明管理员已在有风险的用户 UI 或使用 riskyUsers API 中选择了 "确认用户泄露"。 若要查看已确认此用户已泄露的管理员，请通过 UI 或 API) 检查用户的风险历史记录 (。 |
| 恶意 IP 地址 | Offline | 此检测表明登录是通过恶意 IP 地址进行的。 由于 IP 地址或其他 IP 信誉源接收到无效的凭据，因此 IP 地址会根据高故障率被视为恶意。 |
| 可疑收件箱操作规则 | Offline | 此检测由 [Microsoft Cloud App Security (MCAS) ](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-manipulation-rules)发现。 这项检测会对环境进行分析，并在用户收件箱中设置删除或移动邮件或文件夹的可疑规则时触发警报。 此检测可能表示用户的帐户被泄露，消息将被有意隐藏，邮箱将用于在组织中分发垃圾邮件或恶意软件。 |
| Impossible travel | Offline | 此检测由 [Microsoft Cloud App Security (MCAS) ](/cloud-app-security/anomaly-detection-policy#impossible-travel)发现。 此项检测可识别来源于保持一定地理距离的位置的两个用户活动（一个或多个会话），而完成这两个活动的时间段短于该用户从第一个位置旅行到第二个位置所需的时间，这表示另一个用户在使用相同的凭据。 |

### <a name="other-risk-detections"></a>其他风险检测

| 风险检测 | 检测类型 | 说明 |
| --- | --- | --- |
| 检测到其他风险 | 实时或脱机 | 此检测表明已检测到上述某个高级检测。 由于高级检测仅对 Azure AD Premium P2 客户可见，因此，对于没有 Azure AD Premium P2 许可证的客户，其标题为 "检测到其他风险"。 |

## <a name="common-questions"></a>常见问题

### <a name="risk-levels"></a>风险级别

标识保护将风险分为三个级别：低、中和高。 

尽管 Microsoft 不提供有关如何计算风险的具体详细信息，但我们会说，每个级别都使用户或登录受损的置信度更高。 例如，类似于用户的不熟悉登录属性的一个实例可能不像其他用户泄露的凭据。

### <a name="leaked-credentials"></a>凭据泄露

#### <a name="where-does-microsoft-find-leaked-credentials"></a>Microsoft 在哪里找到泄漏的凭据？

Microsoft 在各种位置查找泄漏的凭据，包括：

- 诸如 pastebin.com 和 paste.ca 之类的公共粘贴站点通常会发布此类材料。 此位置是最糟糕的参与者首次停止查找以查找窃取的凭据。
- 执法机构。
- Microsoft 的其他组进行了深色的 web 研究。

#### <a name="why-arent-i-seeing-any-leaked-credentials"></a>为什么看不到任何泄露的凭据？

当 Microsoft 查找一个新的公开可用批时，将处理泄漏的凭据。 由于机密性质，泄漏的凭据会在处理后很快删除。 启用密码哈希同步后，只会找到新的已泄漏凭据 (PHS) 将针对你的租户进行处理。 不会对先前找到的凭据对执行验证。 

#### <a name="i-havent-seen-any-leaked-credential-risk-events-for-quite-some-time"></a>我未看到任何泄漏的凭据风险事件太长时间？

如果你未看到任何泄漏的凭据风险事件，则原因如下：

- 你没有为租户启用 PHS。
- Microsoft 找不到任何与你的用户相符的泄露凭据对。

#### <a name="how-often-does-microsoft-process-new-credentials"></a>Microsoft 处理新凭据的频率如何？

凭据会在找到后立即处理，通常以多个批处理每天的方式进行处理。

## <a name="next-steps"></a>后续步骤

- [可用于规避风险的策略](concept-identity-protection-policies.md)
- [安全性概述](concept-identity-protection-security-overview.md)
