
原文地址：https://github.com/apple/swift/blob/master/docs/Runtime.md


本文介绍了Swift runtime的ABI接口，接口为Swift程序提供的核心功能如下：

* 内存管理，包括内存分配和引用计数
* runtime的类型系统，包括动态类型转换、泛型实例化、协议一致性注入

本文只是介绍编译器生成代码应该遵守的ABI接口协议，而不会详细描述内部实现细节。

ABI接口目前还处于开发阶段，Swift 3的目标之一是使ABI接口稳定。本文想介绍当前ABI接口的情况以及未来稳定后的ABI接口。在稳定前还需要修改的ABI接口会以**ABI TODO**标记。只存在于使用ObjC交互的[Darwin平台](https://github.com/apple/darwin-xnu)的入口函数和标明只与ObjC交互有关的入口函数，会以**ObjC-only**标记。

## 已废弃的入口函数

这部分的入口函数会在ABI稳定前被删除或者变为私有。

* 导出的C++符号

**ABI TODO**：所有导出的C++符号都属于实现细节，未来稳定的ABI接口不会包含这部分实现细节。

* swift_ClassMirror_count
* swift_ClassMirror_quickLookObject
* swift_ClassMirror_subscript
* swift_EnumMirror_caseName
* swift_EnumMirror_count
* swift_EnumMirror_subscript
* swift_MagicMirrorData_objcValue
* swift_MagicMirrorData_objcValueType
* swift_MagicMirrorData_summary
* swift_MagicMirrorData_value
* swift_MagicMirrorData_valueType
* swift_ObjCMirror_count
* swift_ObjCMirror_subscript
* swift_StructMirror_count
* swift_StructMirror_subscript
* swift_TupleMirror_count
* swift_TupleMirror_subscript
* swift_reflectAny

**ABI TODO**：上述函数是标准库`reflect`具体实现细节接口。这部分函数会被底层runtime的反射API替代。

* swift_stdlib_demangleName

```
@convention(thin) (string: UnsafePointer<UInt8>,

                   length: UInt,
                   
                   @out String) -> ()
```

输入Swift编译后的符号名会返回编译前的名称，比如，输入指针指向是一个带有`length`属性的字符串，返回会是`Swift.String`。

**ABI TODO**：从标准库`Swift.String`的具体实现中解耦，并且去掉stdlib重新命名。

## 内存分配

#### TODO

```
000000000001cb30 T _swift_allocBox

000000000001cb30 T _swift_allocEmptyBox

000000000001c990 T _swift_allocObject

000000000001ca60 T _swift_bufferAllocate

000000000001ca90 T _swift_bufferHeaderSize

000000000001cd30 T _swift_deallocBox

000000000001d490 T _swift_deallocClassInstance

000000000001cd60 T _swift_deallocObject

000000000001cd60 T _swift_deallocUninitializedObject

000000000001d4c0 T _swift_deallocPartialClassInstance

000000000001d400 T _swift_rootObjCDealloc

000000000001c960 T _swift_slowAlloc

000000000001c980 T _swift_slowDealloc

000000000001ce10 T _swift_projectBox

000000000001ca00 T _swift_initStackObject
```

#### 引用计数

#### * swift_retainCount

```
@convention(c) (@unowned NativeObject) -> UInt
```

返回一个随机数，仅会被内存分析工具中使用。

#### * TODO

```

0000000000027ba0 T _swift_bridgeObjectRelease

0000000000027c50 T _swift_bridgeObjectRelease_n

0000000000027b50 T _swift_bridgeObjectRetain

0000000000027be0 T _swift_bridgeObjectRetain_n

000000000001ce70 T _swift_release

000000000001cee0 T _swift_release_n

000000000001ce30 T _swift_retain

000000000001ce50 T _swift_retain_n

000000000001d240 T _swift_tryRetain

0000000000027b10 T _swift_unknownObjectRelease

0000000000027a70 T _swift_unknownObjectRelease_n

0000000000027ad0 T _swift_unknownObjectRetain

0000000000027a10 T _swift_unknownObjectRetain_n

0000000000027d50 T _swift_unknownObjectUnownedAssign

00000000000280a0 T _swift_unknownObjectUnownedCopyAssign

0000000000027fd0 T _swift_unknownObjectUnownedCopyInit

0000000000027ed0 T _swift_unknownObjectUnownedDestroy

0000000000027cb0 T _swift_unknownObjectUnownedInit

0000000000027f20 T _swift_unknownObjectUnownedLoadStrong

00000000000281f0 T _swift_unknownObjectUnownedTakeAssign

0000000000028070 T _swift_unknownObjectUnownedTakeInit

0000000000027f70 T _swift_unknownObjectUnownedTakeStrong

00000000000282b0 T _swift_unknownObjectWeakAssign

0000000000028560 T _swift_unknownObjectWeakCopyAssign

00000000000284e0 T _swift_unknownObjectWeakCopyInit

00000000000283e0 T _swift_unknownObjectWeakDestroy

0000000000028270 T _swift_unknownObjectWeakInit

0000000000028420 T _swift_unknownObjectWeakLoadStrong

0000000000028610 T _swift_unknownObjectWeakTakeAssign

0000000000028520 T _swift_unknownObjectWeakTakeInit

0000000000028470 T _swift_unknownObjectWeakTakeStrong

000000000001d3c0 T _swift_unownedCheck

000000000001cfb0 T _swift_unownedRelease

000000000001d0a0 T _swift_unownedRelease_n

000000000001cf70 T _swift_unownedRetain

000000000001cf60 T _swift_unownedRetainCount

000000000001d2b0 T _swift_unownedRetainStrong

000000000001d310 T _swift_unownedRetainStrongAndRelease

000000000001d060 T _swift_unownedRetain_n

000000000001ca20 T _swift_verifyEndOfLifetime

000000000001d680 T _swift_weakAssign

000000000001d830 T _swift_weakCopyAssign

000000000001d790 T _swift_weakCopyInit

000000000001d770 T _swift_weakDestroy

000000000001d640 T _swift_weakInit

000000000001d6d0 T _swift_weakLoadStrong

000000000001d8b0 T _swift_weakTakeAssign

000000000001d800 T _swift_weakTakeInit

000000000001d710 T _swift_weakTakeStrong

000000000002afe0 T _swift_isUniquelyReferencedNonObjC

000000000002af50 T _swift_isUniquelyReferencedNonObjC_nonNull

000000000002b060 T _swift_isUniquelyReferencedNonObjC_nonNull_bridgeObject

000000000002af00 T _swift_isUniquelyReferenced_native

000000000002aea0 T _swift_isUniquelyReferenced_nonNull_native

00000000000????? T _swift_setDeallocating

000000000001d280 T _swift_isDeallocating
```

**ABI TODO**：_unsynchronized retain/release entry points

## Error对象

`ErrorType`是已经存在的类型，它是一种特殊的单一字符和引用计数表现层。

 
**ObjC-only**：`ErrorType`是存在于runtime内部的，目的是为 `NSError` 和 `CFError`的执行提供有效的桥接。在没有ObjC的平台是不需要这种桥接的，并且设计一个健壮的错误对象接口会更加苦难。

 
为了保持对ErrorType表现层的封装，并且允许未来对表现层优化。runtime为allocating、 projecting reference counting error values 提供了特殊的入口函数。

```
00000000000268e0 T _swift_allocError

0000000000026d50 T _swift_bridgeErrorTypeToNSError

0000000000026900 T _swift_deallocError

0000000000027120 T _swift_errorRelease

0000000000027100 T _swift_errorRetain

0000000000026b80 T _swift_getErrorValue
```

**ABI TODO**: `_unsynchronized` retain/release entry points

**ABI TODO**: `_n` retain/release entry points

## 初始化

#### swift_once

`@convention(thin) (Builtin.RawPointer, @convention(thin) () -> ()) -> ()`
 
用于懒加载全局变量。第一个参数需要指向占用一个word-sized内存地址，这块地址会在进程启动时被初始化为零。在进程的生命周期中，如果引用的内存是被非零值初始化的或者被除swift_once以外的方法写入过，这类行为会被认为是未定义的。第二个参数是一个函数引用，会在进程启动到函数返回间运行一次。

## 动态类型转换
```

0000000000001470 T _swift_dynamicCast

0000000000000a60 T _swift_dynamicCastClass

0000000000000ae0 T _swift_dynamicCastClassUnconditional

0000000000028750 T _swift_dynamicCastForeignClass

000000000002ae20 T _swift_dynamicCastForeignClassMetatype

000000000002ae30 T _swift_dynamicCastForeignClassMetatypeUnconditional

0000000000028760 T _swift_dynamicCastForeignClassUnconditional

00000000000011c0 T _swift_dynamicCastMetatype

0000000000000cf0 T _swift_dynamicCastMetatypeToObjectConditional

0000000000000d20 T _swift_dynamicCastMetatypeToObjectUnconditional

00000000000012e0 T _swift_dynamicCastMetatypeUnconditional

00000000000286c0 T _swift_dynamicCastObjCClass

0000000000028bd0 T _swift_dynamicCastObjCClassMetatype

0000000000028c00 T _swift_dynamicCastObjCClassMetatypeUnconditional

0000000000028700 T _swift_dynamicCastObjCClassUnconditional

0000000000028af0 T _swift_dynamicCastObjCProtocolConditional

0000000000028a50 T _swift_dynamicCastObjCProtocolUnconditional

0000000000028960 T _swift_dynamicCastTypeToObjCProtocolConditional

00000000000287d0 T _swift_dynamicCastTypeToObjCProtocolUnconditional

0000000000000de0 T _swift_dynamicCastUnknownClass

0000000000000fd0 T _swift_dynamicCastUnknownClassUnconditional
```

## Debugging

`0000000000027140 T _swift_willThrow`

## Objective-C Bridging

#### ObjC-only.

**ABI TODO**: 会尽量从rumtime中解耦出来，现在这些都可以在标准库中实现。

```
0000000000003c80 T _swift_bridgeNonVerbatimFromObjectiveCConditional

00000000000037e0 T _swift_bridgeNonVerbatimToObjectiveC

00000000000039c0 T _swift_getBridgedNonVerbatimObjectiveCType

0000000000003d90 T _swift_isBridgedNonVerbatimToObjectiveC
```

## Code generation

runtime会在代码体积优化的过程中确认公共代码路径。

```

0000000000023a40 T _swift_assignExistentialWithCopy

000000000001dbf0 T _swift_copyPOD

000000000001c560 T _swift_getEnumCaseMultiPayload

000000000001c400 T _swift_storeEnumTagMultiPayload
```

## 类型元数据查找

结构化类型、泛型、类和导入的C类型和Objective-C类型的元数据，如果在下列函数中被使用到，需要保证他们已经在runtime中被实例化或初始化。

**ABI TODO**: flux中的初始化API是可回溯工作（resilience work）的一部分。对于标明类型（nominal types），`getGenericMetadata`可能成为实现细节来执行可复原成原类型的元数据的访问函数（resilient per-type metadata accessor functions）。

```
0000000000023230 T _swift_getExistentialMetatypeMetadata

0000000000023630 T _swift_getExistentialTypeMetadata

0000000000023b90 T _swift_getForeignTypeMetadata

000000000001ef30 T _swift_getFunctionTypeMetadata

000000000001eed0 T _swift_getFunctionTypeMetadata1

000000000001f1f0 T _swift_getFunctionTypeMetadata2

000000000001f250 T _swift_getFunctionTypeMetadata3

000000000001e940 T _swift_getGenericMetadata

0000000000022fd0 T _swift_getMetatypeMetadata

000000000001ec50 T _swift_getObjCClassMetadata

000000000001e6b0 T _swift_getResilientMetadata

0000000000022260 T _swift_getTupleTypeMetadata

00000000000225a0 T _swift_getTupleTypeMetadata2

00000000000225d0 T _swift_getTupleTypeMetadata3

0000000000028bc0 T _swift_getInitializedObjCClass
```

**ABI TODO**:`getExistential*TypeMetadata1-3`是快捷入口函数，Any和AnyObject的静态元数据也可能值得思考。

## 类型元数据初始化

Runtime在实例化类型元数据的时候会调用下面的入口函数。

**ABI TODO**: flux中的实例化API是可回溯工作（resilience work）的一部分

```
000000000001e3e0 T _swift_allocateGenericClassMetadata

000000000001e620 T _swift_allocateGenericValueMetadata

0000000000022be0 T _swift_initClassMetadata_UniversalStrategy

000000000001c100 T _swift_initEnumMetadataMultiPayload

000000000001bd60 T _swift_initEnumMetadataSingleCase

000000000001bd60 T _swift_initEnumMetadataSinglePayload

0000000000022a20 T _swift_initStructMetadata

0000000000024230 T _swift_initializeSuperclass

0000000000028b60 T _swift_instantiateObjCClass
```
## Metatypes
```
0000000000000b60 T _swift_getDynamicType

0000000000022fb0 T _swift_getObjectType

00000000000006f0 T _swift_getTypeName

00000000000040c0 T _swift_isClassType

0000000000003f50 T _swift_isClassOrObjCExistentialType

0000000000004130 T _swift_isOptionalType

00000000000279f0 T _swift_objc_class_usesNativeSwiftReferenceCounting

000000000002b340 T _swift_objc_class_unknownGetInstanceExtents

000000000002b350 T _swift_class_getInstanceExtents

0000000000004080 T _swift_class_getSuperclass
```
**ABI TODO**: getTypeByName 入口函数

**ABI TODO**:应该写一个带有已定义的枚举的`getTypeKind`入口函数代替`swift_is*Type`

**ABI TODO**:使用形容词来替换名词命名空间

## 协议一致性查找
```
0000000000002ef0 T _swift_registerProtocolConformances

0000000000003060 T _swift_conformsToProtocol
```

## Error reporting
```
000000000001c7d0 T _swift_reportError

000000000001c940 T _swift_deletedMethodError
```
## 标准元数据

Swift rumtime可以把标准元数据对象导出成`Builtin` 类型和标准的standard value witness tables ，这些可以被具有通用布局（common layout ）属性的类型自由使用。注意，不同于公共类型，runtime不能保证`Builtin`类型和元数据对象是一一对应的，同时runtime会在遇到有相同布局特性的`Builtin`类型时重用元数据对象。

```
000000000004faa8 S __TMBB

000000000004fab8 S __TMBO

000000000004f9f8 S __TMBb

000000000004f9c8 S __TMBi128_

000000000004f998 S __TMBi16_

000000000004f9d8 S __TMBi256_

000000000004f9a8 S __TMBi32_

000000000004f9b8 S __TMBi64_

000000000004f988 S __TMBi8_

000000000004f9e8 S __TMBo

000000000004fac8 S __TMT_

000000000004f568 S __TWVBO

000000000004f4b0 S __TWVBb

000000000004f0a8 S __TWVBi128_

000000000004eec8 S __TWVBi16_

000000000004f148 S __TWVBi256_

000000000004ef68 S __TWVBi32_

000000000004f008 S __TWVBi64_

000000000004ee28 S __TWVBi8_

000000000004f1e8 S __TWVBo

000000000004f778 S __TWVFT_T_

000000000004f3f8 S __TWVMBo

000000000004f8e8 S __TWVT_

000000000004f830 S __TWVXfT_T_

000000000004f620 S __TWVXoBO

000000000004f2a0 S __TWVXoBo

000000000004f6d8 S __TWVXwGSqBO_

000000000004f358 S __TWVXwGSqBo_
```

## Tasks

* 使用per-type实例化函数来代替`getGenericMetadata`
* ObjC约定使用`swift_objc_`命名
* Alternative ABIs for retain/release
* Unsynchronized retain/release
* Nonnull retain/release
* 从标准库中解耦动态类型转换、桥接、反射


