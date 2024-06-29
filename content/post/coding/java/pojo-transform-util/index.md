+++
draft = false
author = "CPoet"
title = "优雅的Pojo转换类封装，基于BeanUtils的链式封装（Transformer类）"
date = " 2022-01-08T21:01:24+08:00"
description = ""
tags = ["Java", "Spring"]
categories = [
    "coding/java",
]
+++

# 前言
> 现实开发中经常遇到Pojo间的转换，手动写Conversion方法过于繁琐，而且代码中也会有很多冗余代码。为了偷懒干脆直接使用`BeanUtils.copyProperties`方法，可是每次结合**lambda**和链式时，总觉得直接使用BeanUtils中断链式不太舒服，因此封装了`Transformer`类。
# Transformer类
```java
/**
 * POJO转换便捷类
 *
 * @author wanggf
 */
public final class Transformer<SOURCE, TARGET> {
    private final Supplier<TARGET> supplier;
    private BiConsumer<SOURCE, TARGET> consumer;

    /**
     * 构造器
     *
     * @param supplier 目标对象工厂
     */
    public Transformer(Supplier<TARGET> supplier) {
        this.supplier = supplier;
    }

    /**
     * 自定义填充方法
     * <p>链式使用该填充方法时需注意，如果编译器无法推断出{@link SOURCE}的类型，则可以显式指示该类型。</p>
     * <pre>new Transformer&lt;User, UserVo&get;(UserVo::new).fill((s, t) -> t.setUserId(s.getUserId))</pre>
     *
     * @param consumer 填充方法
     * @return 便捷类实例
     */
    public Transformer<SOURCE, TARGET> fill(BiConsumer<SOURCE, TARGET> consumer) {
        this.consumer = this.consumer == null ? consumer : this.consumer.andThen(consumer);
        return this;
    }

    /**
     * 自定义填充方法
     *
     * @param consumer 填充方法
     * @return 便捷类实例
     */
    public Transformer<SOURCE, TARGET> fill(Consumer<TARGET> consumer) {
        return fill((s, t) -> consumer.accept(t));
    }

    /**
     * 目标转换
     *
     * @param map 转换方法
     * @param <T> 目标类型
     * @return 新构造的便捷类实例
     */
    public <T> Transformer<TARGET, T> map(Function<TARGET, T> map) {
        return new Transformer<>(() -> map.apply(supplier.get()));
    }

    /**
     * 执行转换
     *
     * @param source 源对象
     * @return 目标对象
     */
    public TARGET transform(SOURCE source) {
        TARGET target = supplier.get();
        if (target != null && source != null) {
            BeanUtils.copyProperties(source, target);
            if (consumer != null) {
                consumer.accept(source, target);
            }
        }
        return target;
    }

    /**
     * 执行转换，并返回{@link Optional}对象
     *
     * @param source 源对象
     * @return {@link Optional}
     */
    public Optional<TARGET> transformDoOpt(SOURCE source) {
        return Optional.ofNullable(transformTo(source));
    }
}
```
# 示例
## Transformer单对象转换
```java
public CategoryVo getById(Long id) {
     Category category = iCategoryService.getById(id);
     if (category == null) {
         throw new RequestException(MostReturnStatus.NONEXISTENT_RESOURCE, "文章分类不存在");
     }
     return new Transformer<>(CategoryVo::new).fill(t -> {
         if (!Objects.equals(t.getParentId(), DataDefaultValueConst.DEFAULT_PARENT_ID)) {
             t.setParentName(getCategoryNameById(t.getParentId()));
         }
     }).transform(category);
 }
```
## Transformer集合转换
```java
public PageVo<PersonCategoryVo> listByTerm(CategoryParam categoryParam, PageParam pageParam, Subject subject) {
    PageVo<Category> categories = iCategoryService
        .listByName(categoryParam.getName(), subject.getUid(), pageParam.getDecrPage(), pageParam.getSize());
    return categories.map(new Transformer<>(PersonCategoryVo::new).fill(t -> {
        if (!Objects.equals(t.getParentId(), DataDefaultValueConst.DEFAULT_PARENT_ID)) {
            t.setParentName(getCategoryNameById(t.getParentId()));
        }
    })::transform);
}
```
