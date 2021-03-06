---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Freshservice 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Freshservice 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3dd22b1f-445d-45c6-8eda-30207eb9a1a8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 07/29/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 06b52ec552434f74c56333d1afe00cde2a285418
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87905391"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-freshservice"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Freshservice 集成

本教程介绍如何将 Freshservice 与 Azure Active Directory (Azure AD) 集成。 将 Freshservice 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Freshservice。
* 让用户使用其 Azure AD 帐户自动登录到 Freshservice。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Freshservice 单一登录 (SSO) 的订阅。

> [!NOTE]
> 此集成也可以通过 Azure AD 美国国家云环境使用。 你可以在“Azure AD 美国国家云应用程序库”中找到此应用程序，并以与在公有云中相同的方式对其进行配置。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Freshservice 支持 **SP** 发起的 SSO
* 配置 Freshservice 后，就可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-freshservice-from-the-gallery"></a>从库中添加 Freshservice

若要配置 Freshservice 与 Azure AD 的集成，需要从库中将 Freshservice 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务。
1. 导航到“企业应用程序”，选择“所有应用程序” 。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Freshservice” 。
1. 从结果面板中选择“Freshservice”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-freshservice"></a>配置并测试 Freshservice 的 Azure AD 单一登录

使用名为“B.Simon”的测试用户为 Freshservice 配置和测试 Azure AD SSO。 若要使 SSO 有效，需要在 Azure AD 用户与 Freshservice 相关用户之间建立关联。

若要配置和测试 Freshservice 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Freshservice SSO](#configure-freshservice-sso)** - 在应用程序端配置单一登录。
    1. **[创建 Freshservice 测试用户](#create-freshservice-test-user)** - 在 Freshservice 中创建 B. Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“Freshservice”应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值：

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<democompany>.freshservice.com`。

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<democompany>.freshservice.com`

    > [!NOTE]
    > 这些不是实际值。 使用实际登录 URL 和标识符更新这些值。 请联系 [Freshservice 客户端支持团队](https://support.freshservice.com/)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

1. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“Azure 门户”上的“设置 Freshservice”部分中，根据要求复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”  。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com`。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。
   1. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过向 B.Simon 授予对 Freshservice 的访问权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Freshservice”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-freshservice-sso"></a>配置 Freshservice SSO

1. 打开新的 Web 浏览器窗口，以管理员身份登录到 Freshservice 公司站点。

1. 在左侧菜单中，单击“管理”，然后在“常规设置”中选择“支持人员安全性”。

    ![管理员](./media/freshservice-tutorial/configure-1.png "管理员")

1. 在“安全性”中，单击“转到 Freshworks 360 安全性”。

    ![安全性](./media/freshservice-tutorial/configure-2.png "安全性")

1. 在“安全”部分中，执行以下步骤：

    ![单一登录](./media/freshservice-tutorial/configure-3.png "单一登录")
  
    a. 对于“单一登录”，请选择“启用”。 

    b. 在“登录方法”中，选择“SAML SSO”。

    c. 在“IdP 提供的实体 ID”文本框中，粘贴从 Azure 门户复制的“实体 ID”值 。

    d. 在“SAML SSO URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值 。

    e. 在“签名选项”中，从下拉菜单中选择“仅限已签名的断言”。

    f. 在“注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值 。

    g. 在“安全证书”文本框中，粘贴你之前获得的“证书(Base64)”值。
  
    h. 单击“ **保存**”。


## <a name="create-freshservice-test-user"></a>创建 Freshservice 测试用户

若要使 Azure AD 用户能够登录 FreshService，必须将这些用户预配到 FreshService 中。 对于 FreshService，需要手动执行预配。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 **FreshService** 公司站点。

2. 在左侧菜单中，单击“管理”。

3. 在“用户管理”部分，单击“请求者” 。

    ![申请者](./media/freshservice-tutorial/create-user-1.png "请求者")

4. 单击“新建申请者”。

    ![新建申请者](./media/freshservice-tutorial/create-user-2.png "新建请求者")

5. 在“新建请求者”部分中，输入必填字段，然后单击“保存”。
    ![新建申请者](./media/freshservice-tutorial/create-user-3.png "新建请求者")  

    > [!NOTE]
    > Azure Active Directory 帐户持有者将收到一封电子邮件，其中包含用于在激活帐户前确认帐户的链接
    >  

    > [!NOTE]
    > 可以使用 FreshService 提供的任何其他 FreshService 用户帐户创建工具或 API 来预配 Azure AD 用户帐户。

## <a name="test-sso"></a>测试 SSO

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Freshservice 磁贴时，应当会自动登录到你为其设置了 SSO 的 Freshservice。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [试用 Freshservice 与 Azure AD](https://aad.portal.azure.com/)