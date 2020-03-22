# ���������ӻ�
�����������ָ���ݴ�ǰ�������˵Ĺ�����;���������������˵���񣬱����û���http�����ȵ�Nginx, �ٵ���˷���1, �ٵ����ַ����ٵ���������ٵ���˷���2, Ȼ�������ݿ⣬�Լ��������ã��ܵ���˵����һ����������·���������������һ�����̿��ӻ������������ǲ���ģ���������һ�����ӻ�Ҳ�Ϳ�������һ����صĿ��ӻ�����ز��Ե������Ƿ������ɵ������Ӧ�Լ������Ǹ�������������⡣

�������ǰ�˺�˲��������ڿ����е�ǰ�˺�ˣ�ֻ�������������ĵط����������������粻ͬ������뽻������·����֮���������������������֮��������������ܵ���˵���е������ܻ���һ�������ķ������������ͨ��һ���ļ�����ȡÿ���ڵ�ı�־����ô�Ϳ��Խ�����·����̬�Ŀ��ӻ�������


## ��ʾЧ��
![image](img/dataflow-vis.gif)

## �����ܹ�
����ѡ��JavaScript��SVG, ֮����ѡ��SVG����ΪD3�Լ��ж�Ӧ��layout��,���Կ��Ժܷ���Ľ����ݽ�����״�Ŀ��ӻ���

- D3.js



## ����
�����������Ҫ��ص��������ܹ�����
```

                                            |---> backend11
           |---> nginx1 ---> backend1 --->  |
           |                                |---> backend12
client --> |
           |                                |---> backend21
           |---> nginx2 ---> backend2 --->  |
                                            |---> backend22
```


��ô���ǿ������������ַ�ʽ����ʾ
```
# ��ÿһ����Ϊһ���ڵ�,Ȼ��ͨ��parentָ���Ӧ��parent�ڵ�
rawData1 = [
    {name: "client", parent: "", status: 1},
    {name: "nginx1", parent: "client", status: 1},
    {name: "nginx2", parent: "client", status: 1},
    {name: "backend1", parent: "nginx1", status: 1},
    {name: "backend2", parent: "nginx2", status: 1},
    {name: "backend11", parent: "backend1", status: 1},
    {name: "backend12", parent: "backend1", status: 1},   
    {name: "backend21", parent: "backend2", status: 1},
    {name: "backend22", parent: "backend2", status: 1},   
]

# ͨ��Ƕ�׵�json���ݽṹ����ʾ
rawData2 = {data:{id: "client"},
    children: [
        {data: {id: "nginx1"},
         children: [
             {data: {id: "backend1"},
              children: [
                  {data: {id: "backend11"}},
                  {data: {id: "backend12"}}
              ]
            }
         ]
        },
        {data: {id: "nginx2"},
         children: [
             {data: {id: "backend2"},
              children: [
                  {data: {id: "backend21"}},
                  {data: {id: "backend22"}}
              ]
            }
         ]
        }
]};

```

## ������״ͼ
ͨ��D3.js������״ͼ��Ҫ��Ϊ2������
1. ������ת����D3.js�ܹ�ʶ�����ʽ�������ᵽ��������ʽD3.js���ǿ��Ժܺô���ġ�
2. ������״ͼ�ĸ������֣��ڵ㣬���ڵ�֮������ߣ����ڵ�ı�ע���֡�
> ��״ͼ�ĸ������ֿ��Կ��Լ���Ҫ������Ӧ�Ĳ��֣����粻���ƽڵ�ı�ʶ�������֡�

### ��������
ͨ��d3ͨ����stratify��������һ����ʽ�����ݴ���ɿ���ֱ�Ӵ���d3.tree�����������ʽ
```
var rawData = [
    {name: "client", parent: "", status: 1},
    {name: "nginx1", parent: "client", status: 1},
    {name: "nginx2", parent: "client", status: 1},
    {name: "backend1", parent: "nginx1", status: 1},
    {name: "backend2", parent: "nginx2", status: 1},
    {name: "backend11", parent: "backend1", status: 1},
    {name: "backend12", parent: "backend1", status: 1},   
    {name: "backend21", parent: "backend2", status: 1},
    {name: "backend22", parent: "backend2", status: 1},   
]

// stratify������ɵ����ݣ�������Ҫ�����ֶ�: id, children
// ͨ����ʽ�ĵ���id����, ���Դ���һ���ص��������������е�ÿһ�У�����Ӧ�÷�����һ��������Ϊid����ֵ
// ͨ����ʽ�ĵ���parentId, ���Դ���һ���ص��������������е�ÿһ�У�����Ӧ�÷�����һ��������ָ���parent id
const data = d3.stratify()
                 .id(d => d.name)
                 .parentId(d => d.parent)
                 (rawData);
                 
                 
```
ͨ��d3ͨ����hierarchy�������ڶ�����ʽ�����ݴ���ɿ���ֱ�Ӵ���d3.tree�����������ʽ
```
var rawData = {data:{id: "client"},
                    children: [
                        {data: {id: "nginx1"},
                         children: [
                             {data: {id: "backend1"},
                              children: [
                                  {data: {id: "backend11"}},
                                  {data: {id: "backend12"}}
                              ]
                            }
                         ]
                        },
                        {data: {id: "nginx2"},
                         children: [
                             {data: {id: "backend2"},
                              children: [
                                  {data: {id: "backend21"}},
                                  {data: {id: "backend22"}}
                              ]
                            }
                         ]
                        }
                    ]};
                    
// ��������������Ѿ��ж�Ӧ��id, children�ֶΣ����Բ���Ҫ����Ĵ�������ֱ�Ӵ���hierarchy.
const data = d3.hierarchy(rawData)
```

������hierarchy����stratify���ǽ�����ת����һ��Ƕ�׵Ķ��󣬶���Ľṹ��һ������

Ч������

![image](img/data.png)

### ������״ͼ�ĸ�������
��ʼ�������󣬽����洦����ɵ����ݿ���ֱ�Ӵ�����ʼ�����d3.tree���󡣽�data���ݵ��ú��õ�һ��tree�Ķ���(���������root)����������������Ƚ���Ҫ������, links, descendants.

���Ƿֱ��Ӧ����״�ṹ�ĸ����ߵ�·���Լ��ڵ��λ�á�

```
const myTree = d3.tree().size([innerWidth - 400, innerHeight]);
const root = myTree(data);
const links = root.links();
const nodes = root.descendants();
```
> ֮���Խ�innerWidth��ȥ400,����Ϊ������õ�tree��λ�ü��㷽����������ݽṹ������ͼ�ı����������ǻ����Ĵ�С������������ܵ���˵�������Ҫ�����Ŀ��ӻ�ͼ�ŵ�һ�����ʵ�λ�ã���������Ҫ�������ݽṹ����һ�£���Ҷ���һ�¾��˽��ˡ�


��������ÿ����
```
// ����links
svg.selectAll("path").data(links)
    .enter().append("path")
        .attr("d", d3.linkHorizontal()
            .x(d => d.y)
            .y(d => d.x)
        )

```

��������ÿ���ڵ��Լ�����
```
// ���ƽڵ�
const circles = g.selectAll("circle").data(nodes, d=>d.data.id)
const circleFill = d => d.data.status ? successColor:failedColor

circles
    .enter().append("circle")
        .attr("r", 0)
        // ע��!x ���� y, �������
        .attr("cx", d => d.y)
        .attr("cy", d => d.x)
        .attr("fill", circleFill)


// ��������
g.selectAll("text").data(nodes)
    .enter().append("text")
        .attr("x", d => d.y)
        .attr("y", d => d.x)
        // ����ýڵ���children�ֶΣ���˵�����ӽڵ㣬��ôx�����λ�ò���
        // ��֮��λ�������ƶ�10������
        .attr("dx", d => d.children? 0: 10)
        // ����ýڵ���children�ֶΣ���˵�����ӽڵ㣬��ôy�����λ�������ƶ�10������
        // ��֮��λ�������ƶ�5������
        .attr("dy", d => d.children? -10: 5)
        // ����ýڵ���û��parent,��˵����root�ڵ㣬���ֵĶ��䷽ʽΪend
        // ����ýڵ���children�ֶΣ���˵�����ӽڵ㣬���ֵĶ��䷽ʽΪmiddle
        // ��֮�����ֵĶ��䷽ʽΪmiddle
        .attr("text-anchor", d => {
            if (!d.parent) {
                return "end"
            } else if (d.children) {
                return "middle"
            } else {
                return "start"
            }
        })
        .attr("font-size",  "1em")
        .text(d=> d.data.name)
```

### �����ĸ����߶�����
������Ч����Ϊ��������ʵ�֣�һ����ԭ��·���ϼ�һ��Path,���������pathͨ��css��������


��������������
```
// ��������������
g.selectAll("path.flow").data(links)
    .enter().append("path")
        // ע��!x ���� y, �������
        // ͨ��linkHorizontal��links���������ת����ͼ�ж�Ӧ��path�Ļ���·��
        .attr("d", d3.linkHorizontal()
            .x(d => d.y)
            .y(d => d.x)
        )
        .attr("class", "flow")
```

����css��ʽ
```
path.flow {
    fill:transparent;
    stroke:#6D9459;
    stroke-dasharray: 20 4;
    animation: flowpath 4s linear infinite;
}

@keyframes flowpath {
    from {
        stroke-dashoffset: 100;
    }

    to {
        stroke-dashoffset: 0;
    }
}
```

����Ч�������¿�ʼ�Ķ���


### �����������ţ��϶��¼�
ͨ������м��Ļ�������ͼ�Ĵ�С��ͨ�����Ҳ�����ƶ�svg����
```
const zoomG = svg.append("g");
const g = zoomG.append("g").attr("transform", `translate(${margin.left} ${margin.top})`);

      // ���������϶��Ĳ���  
      svg.call(d3.zoom().on("zoom", () => {
        zoomG.attr("transform", d3.event.transform)            
    }));
```



## �ܽ�
�������еĴ��벿�ָ�����Ҫ�г������滻����صı���������ֻ�Ǹ������еĴ���������������ģ������Ĵ�����ο�������Դ���롣


## Դ����
https://github.com/youerning/blog/tree/master/dataflow-vis

����ڴ��������¿��Թ�ע�ҵ�΢�Ź��ں�(�ֶ��ʼ�)��ͷ����(�ֶ��ʼ�)��github��



## Q&A
### Q:��ô��ȡ��ȡ�������ĵ�·��?

�����ṩ�����������ļ����Ϊ�ο�:
1. http����

��˿���Ϊ�ض�����������һ��Ψһ��ID, ���ID���ڸ���������ݣ�����ÿ������ڼ�ص�����ض���ID�Ϳ��Խ����ݷ��͵��Լ��ļ�����ģ��������Ϊǰ���ṩ����õ����ݡ�

2. ����֮�����ͨ��

ÿһ���ڵ㶼��Ҫ���õĴ������˵Agent, ��������ڰ����ض���ʱ�������Լ�ֱ����Agent����ping�����ض��������Լ��������ͨ�ԡ�

### Q: ����ʲô��?

�������õľͻ����ðɣ�����û�õľ�û�п���

### Q: ���������½ڵ�֮������ݽṹ������״�ṹ��������ṹ�������ô��?

���ڷ���״�ṹ�ͷ��ں����������˵�ɣ�





