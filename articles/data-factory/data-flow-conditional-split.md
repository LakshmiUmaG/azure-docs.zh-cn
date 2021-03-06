---
title: 映射数据流中的有条件拆分转换
description: 使用 Azure 数据工厂映射数据流中的有条件拆分转换功能将数据拆分为不同的流
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/21/2020
ms.openlocfilehash: eece6f97e82f3800d4f59ac1849b34c2a1e4635b
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800086"
---
# <a name="conditional-split-transformation-in-mapping-data-flow"></a>映射数据流中的有条件拆分转换

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

有条件拆分转换根据匹配条件将数据行路由到不同的流。 有条件拆分转换类似于编程语言中的 CASE 决策结构。 转换计算表达式，并根据结果将数据行定向到指定的流。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4wKCX]

## <a name="configuration"></a>配置

“拆分依据”设置可确定数据行是流向第一个匹配流还是其匹配的每个流。

使用数据流表达式生成器为拆分条件输入表达式。 若要添加新条件，请单击现有行中的加号图标。 还可以为与任何条件都不匹配的行添加默认流。

![有条件拆分](media/data-flow/conditionalsplit1.png "有条件拆分选项")

## <a name="data-flow-script"></a>数据流脚本

### <a name="syntax"></a>语法

```
<incomingStream>
    split(
        <conditionalExpression1>
        <conditionalExpression2>
        ...
        disjoint: {true | false}
    ) ~> <splitTx>@(stream1, stream2, ..., <defaultStream>)
```

### <a name="example"></a>示例

下面的示例是一个名为 `SplitByYear` 的有条件拆分转换，它采用传入流 `CleanData`。 此转换有两个拆分条件，即 `year < 1960` 和 `year > 1980`。 `disjoint` 为 false，因为数据转到了第一个匹配条件。 每个匹配第一个条件的行都会转到输出流 `moviesBefore1960`。 与第二个条件匹配的所有剩余行都会转到输出流 `moviesAFter1980`。 所有其他行都转到默认流 `AllOtherMovies`。

在数据工厂 UX 中，此转换如下图所示：

![有条件拆分](media/data-flow/conditionalsplit1.png "有条件拆分选项")

此转换的数据流脚本位于下面的代码片段中：

```
CleanData
    split(
        year < 1960,
        year > 1980,
        disjoint: false
    ) ~> SplitByYear@(moviesBefore1960, moviesAfter1980, AllOtherMovies)
```

## <a name="next-steps"></a>后续步骤

与条件拆分一起使用的常见数据流转换有[联接转换](data-flow-join.md)、[查找转换](data-flow-lookup.md)和[选择转换](data-flow-select.md)
