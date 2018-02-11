

一、遍历中修改List问题
```java
Collection<String> list = new ArrayList<String>();
        list.add("Android");
        list.add("iPhone");
        list.add("Windows Mobile");

        // example 0
        Iterator<String> itr0 = list.iterator();
        while(itr0.hasNext()){
            String lang = itr0.next();
            itr0.remove();
        }

        // example 1
        Iterator<String> itr1 = list.iterator();
        while(itr1.hasNext()){
            String lang = itr1.next();
            list.remove(lang);
        }

        // example 2
        for(int i = 0; i < list.size(); i++){
            list.remove(i);
        }

        // example 3
        for(String language: list){
            list.remove(language);
        }
```
- example0,example2不会抛出ConcurrentModificationException异常;
- example1,example3则会抛出ConcurrentModificationException异常;

原因分析:
example1没调用Iterator的remove方法进行删除;在itr1.next()中会检查
```java
ourList.modCount != expectedModCount
```
example3的for-each循环内部其实也是使用了Iterator来遍历Collection，它也调用了Iterator.next()，这同样也会检查(元素的)变化并抛出ConcurrentModificationException!
