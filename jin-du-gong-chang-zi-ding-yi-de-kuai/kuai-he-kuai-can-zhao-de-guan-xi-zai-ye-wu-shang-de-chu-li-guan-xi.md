#### 块和块参照在此金都业务中的处理关系

在思考这个问题之前，一定要明白几个问题，第一是为什么要用到块参照，在什么情况下会用到块参照，其次块参照的生成方式，还有这样的生成方式哪些必要的数据我们需要留下，留下的业务数据怎样能最大优化我们的代码，最后，如何在客户端用数据的时候，可以方便形式

其实在任何一个业务功能板块去设计的时候，都应该按照这样的思路去设计class，切不可只是以一个方面去设计，特别是只以功能方面去参考，不然留下的代码只会遗臭万年,hhhh

分析 ： 块是我们自定义的优化设计师操作的自定义好的实体的组合，是一个形状的模子，而我们想要在模型空间上去用它，并且可以做到能有最大可能的使整个模子不是定死的，所以我们用到了块参照这个CAD的特殊属性

理解 ：

现在业务就是根据实际情况来设计逻辑结构，对于块，实体类的定义，我们要做到扩展性的最大化和耦合性的最小化，比较常见和好的办法就是通过反射+配置文件+工厂去决定它的诞生，主要是为了后期肯定会有更多的自定义的块要去生成，对于客户端来说，有了工厂，需要什么块，给他个名字，你就可以给我造，不用我自己去造；

对于块参照来说，实际的应用情况是在设计人员在本地的dwg文件中形成块的定义，然后需要保存的是块的基本信息+自己设计时添加的一些参数，比如缩放的比例，旋转的角度等等，这些就属于自定义的非常灵活的部分属性数据，需要保存在dwg的文件中，因为在下次，或者转交给别的设计师去做其它的业务，比如生成泡沫的excel时，在框选这部分块参照的区域，我们是需要这些信息来解决netload下命令生成数据的算法逻辑结构的

最后的设计：

对于块的定义设计，在本章下的“各种块定义”中有记录

对于块参照的定义，

