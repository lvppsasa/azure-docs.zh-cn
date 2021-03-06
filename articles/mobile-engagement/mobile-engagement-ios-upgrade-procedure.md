---
title: Azure Mobile Engagement iOS SDK 升级过程 | Microsoft Docs
description: Azure Mobile Engagement 的 iOS SDK 的最新更新和过程
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 72a9e493-3f14-4e52-b6e2-0490fd04b184
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 12/13/2016
ms.author: piyushjo
ms.openlocfilehash: ac03593cf0c983297e583985a3e68ea6c17e39de
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2018
---
# <a name="upgrade-procedures"></a>升级过程
> [!IMPORTANT]
> Azure Mobile Engagement 已在 2018 年 3 月 31 日停用。 此页将在不久之后删除。
> 

如果已将较旧版本的 Engagement 集成到应用程序中，则在升级 SDK 时必须考虑以下几点。

对于每个新版本的 SDK，必须首先替换（移除并重新导入 xcode）EngagementSDK 和 EngagementReach 文件夹。

## <a name="from-300-to-400"></a>从 3.0.0 至 4.0.0
### <a name="xcode-8"></a>XCode 8
从 SDK 的版本 4.0.0 开始，XCode 8 就是必需的。

> [!NOTE]
> 如果确实需要使用 XCode 7，则可以使用 [iOS Engagement SDK v3.2.4](https://aka.ms/r6oouh)。 在 iOS 10 设备上运行时，此早期版本的市场宣传模块上存在一个已知 bug：无法操作系统通知。 要修复此问题，必须在应用委派中实现否决的 API `application:didReceiveRemoteNotification:`，如下所示：
> 
> 

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [!IMPORTANT]
> **我们不建议此解决方法**，因为此 iOS API 已被否决，此行为在任何即将发布的（即使再小）iOS 版本升级过程中会有所更改。 你应尽快改用 XCode 8。
> 
> 

### <a name="usernotifications-framework"></a>UserNotifications 框架
你需要在构建阶段添加 `UserNotifications` 框架。

在项目资源管理器中，打开你的项目窗格，并选择正确的目标。 然后，打开“**构建阶段**”选项卡，在“**将二进制文件链接到库**”菜单中添加框架 `UserNotifications.framework` - 将链接设置为 `Optional`

### <a name="application-push-capability"></a>应用程序推送功能
XCode 8 可能会重置你的应用推送功能，请在你选定目标的 `capability` 选项卡中再核实一下。

### <a name="add-the-new-ios-10-notification-registration-code"></a>添加新的 iOS 10 通知注册代码
将应用注册到通知的较旧代码片段仍可使用，但在 iOS 10 上运行时会使用已弃用的 API。

导入 `User Notification` 框架：

        #import <UserNotifications/UserNotifications.h> 

在你的应用程序中，委托 `application:didFinishLaunchingWithOptions` 方法替换︰

    if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
        [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil]];
        [application registerForRemoteNotifications];
    }
    else {

        [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
    }

用：

        if (NSFoundationVersionNumber >= NSFoundationVersionNumber_iOS_8_0)
        {
            if (NSFoundationVersionNumber > NSFoundationVersionNumber_iOS_9_x_Max)
            {
                [UNUserNotificationCenter.currentNotificationCenter requestAuthorizationWithOptions:(UNAuthorizationOptionBadge | UNAuthorizationOptionSound | UNAuthorizationOptionAlert) completionHandler:^(BOOL granted, NSError * _Nullable error) {}];
            }else
            {
                [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert)   categories:nil]];
            }
            [application registerForRemoteNotifications];
        }
        else
        {
            [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
        }

### <a name="resolve-unusernotificationcenter-delegate-conflicts"></a>解决 UNUserNotificationCenter 委托冲突

如果应用程序或其中一个第三方库实现了 `UNUserNotificationCenterDelegate`，则可以跳过此部分。

SDK 使用 `UNUserNotificationCenter` 委托来监视运行 iOS 10 或更高版本的设备上的 Engagement 通知的生命周期。 SDK 具有其自己实现的 `UNUserNotificationCenterDelegate` 协议，但每个应用程序只能有一个 `UNUserNotificationCenter` 委托。 任何其他添加到 `UNUserNotificationCenter` 对象的委托将与 Engagement 委托冲突。 如果 SDK 检测到你或任何其他第三方的委托，则不会使用其自己的实现，从而让你有机会解决此冲突。 需要将 Engagement 逻辑添加到自己的委托中来解决此冲突。

可通过两种方式实现此目的：

方案 1：只需将委托调用转发到 SDK；

    #import <UIKit/UIKit.h>
    #import "EngagementAgent.h"
    #import <UserNotifications/UserNotifications.h>


    @interface MyAppDelegate : NSObject <UIApplicationDelegate, UNUserNotificationCenterDelegate>
    @end

    @implementation MyAppDelegate

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterWillPresentNotification:notification withCompletionHandler:completionHandler]
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterDidReceiveNotificationResponse:response withCompletionHandler:completionHandler]
    }
    @end

方案 2：通过继承自 `AEUserNotificationHandler` 类

    #import "AEUserNotificationHandler.h"
    #import "EngagementAgent.h"

    @interface CustomUserNotificationHandler :AEUserNotificationHandler
    @end

    @implementation CustomUserNotificationHandler

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center willPresentNotification:notification withCompletionHandler:completionHandler];
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse: UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center didReceiveNotificationResponse:response withCompletionHandler:completionHandler];
    }

    @end

> [!NOTE]
> 通过将通知的 `userInfo` 字典传递到代理 `isEngagementPushPayload:` 类方法，你可以确定该通知是否来自于 Engagement。

请确保 `UNUserNotificationCenter` 对象的委托在你的应用程序委托的 `application:willFinishLaunchingWithOptions:` 或 `application:didFinishLaunchingWithOptions:` 方法内设置为你的委托。
例如，如果已实现上述协议 1：

      - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
        // Any other code
  
        [UNUserNotificationCenter currentNotificationCenter].delegate = self;
        return YES;
      }

## <a name="from-200-to-300"></a>从 2.0.0 至 3.0.0
放弃了对 iOS 4.X 的支持。 从此版本开始，应用程序的部署目标必须至少为 iOS 6。

如果在应用程序中使用 Reach，那么为了接收远程通知，必须将 `remote-notification` 值添加到 Info.plist 文件中的 `UIBackgroundModes` 数组。

在应用程序委托中，需要将方法 `application:didReceiveRemoteNotification:` 替换为 `application:didReceiveRemoteNotification:fetchCompletionHandler:`。

AEPushDelegate.h 接口已弃用，需要移除所有引用。 这包括从应用程序委托中移除 `[[EngagementAgent shared] setPushDelegate:self]` 和委托方法：

    -(void)willRetrieveLaunchMessage;
    -(void)didFailToRetrieveLaunchMessage;
    -(void)didReceiveLaunchMessage:(AEPushMessage*)launchMessage;

## <a name="from-1160-to-200"></a>从 1.16.0 至 2.0.0
以下部分介绍如何将 SDK 集成从由 Capptain SAS 提供的 Capptain 服务迁移到 Azure Mobile Engagement 支持的应用。
如果从较早版本进行迁移，请参阅 Capptain 网站先迁移到 1.16，再应用以下过程。

> [!IMPORTANT]
> Capptain 和 Mobile Engagement 不是相同的服务，以下提供的过程仅重点描述如何迁移客户端应用。 迁移应用中的 SDK 不会将数据从 Capptain 服务器迁移到 Mobile Engagement 服务器
> 
> 

### <a name="agent"></a>代理
方法 `registerApp:` 已替换为新方法 `init:`。 应用程序委托必须相应地进行更新，并使用连接字符串︰

            - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
            {
              [...]
              [EngagementAgent init:@"YOUR_CONNECTION_STRING"];
              [...]
            }

已从 SDK 移除 SmartAd 跟踪，只需要移除 `AETrackModule` 类的所有实例

### <a name="class-name-changes"></a>类名更改
作为品牌重塑的一部分，有几个类/文件名需要进行更改。

带有前缀为 CP 的所有类重命名后，前缀都变成了 AE。

示例：

* `CPModule.h` 已重命名为 `AEModule.h`。

带有前缀 Capptain 的所有类重命名后，前缀都变成了 Engagement。

示例:

* 类 `CapptainAgent` 已重命名为 `EngagementAgent`。
* 类 `CapptainTableViewController` 已重命名为 `EngagementTableViewController`。
* 类 `CapptainUtils` 已重命名为 `EngagementUtils`。
* 类 `CapptainViewController` 已重命名为 `EngagementViewController`。

