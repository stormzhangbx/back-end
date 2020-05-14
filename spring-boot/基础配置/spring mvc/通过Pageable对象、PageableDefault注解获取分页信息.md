# 通过Pageable对象、PageableDefault注解获取分页信息

[springmvc 通过 Pageable对象和PageableDefault注解获取分页信息](https://blog.csdn.net/zsg88/article/details/66025560/)

Pageable 是Spring Data库中定义的一个接口，该接口是所有分页相关信息的一个抽象，通过该接口，我们可以得到和分页相关所有信息（例如pageNumber、pageSize等）。

Pageable定义了很多方法，但其核心的信息只有两个：一是分页的信息（page、size），二是排序的信息。

在springmvc的请求中只需要在方法的参数中直接定义一个pageable类型的参数，当Spring发现这个参数时，Spring会自动的根据request的参数来组装该pageable对象，Spring支持的request参数如下：

- page，第几页，从0开始，默认为第0页
- size，每一页的大小，默认为20
- sort，排序相关的信息，以property,property(,ASC|DESC)的方式组织，例如sort=firstname&sort=lastname,desc表示在按firstname正序排列基础上按lastname倒序排列。

这样，我们就可以通过url的参数来进行多样化、个性化的查询。

Spring data提供了@PageableDefault帮助我们个性化的设置pageable的默认配置。例如@PageableDefault(value = 15, sort = { "id" }, direction = Sort.Direction.DESC)表示默认情况下我们按照id倒序排列，每一页的大小为15。

```java
@ResponseBody
@RequestMapping(value = "list", method=RequestMethod.GET)
public Page<blog> listByPageable(@PageableDefault(value = 15, sort = { "id" }, direction = Sort.Direction.DESC) 
    Pageable pageable) {
 
 
    return blogRepository.findAll(pageable);
}
```