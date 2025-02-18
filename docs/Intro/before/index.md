# 写在前面


<div id="dynamic-text-box" style="
    width: 100%;
    max-width: 800px; /* 可根据需要调整最大宽度 */
    margin: 0 auto;
    border: 1px solid #ccc;
    padding: 20px;
    font-size: 16px;
    white-space: pre-wrap;
    background-color: #F9D9CA;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
    border-radius: 8px;
"></div>

<script>
    const textBox = document.getElementById('dynamic-text-box');
    const textToDisplay = "“我—我……我当时感到这辈子有数年时光就这么顺着指缝流失殆尽。\n我从来没有停下来想想自己是否乐在其中……从来没有。\n但是在那一刻，我停下来了……并且意识到自己需要喘息。\n我有哪怕一丁点喜欢这份学业吗？这种质疑令我惊恐不已。\n我竭尽全力，才抑制住自己仓皇逃走的冲动。”";
    let index = 0;

    function typeWriter() {
        if (index < textToDisplay.length) {
            textBox.textContent += textToDisplay.charAt(index);
            index++;
            setTimeout(typeWriter, 100); // 调整速度
        } else {
            setTimeout(deleteText, 1000); // 显示完成后等待1秒开始删除
        }
    }

    // function deleteText() {
    //     if (textBox.textContent.length > 0) {
    //         textBox.textContent = textBox.textContent.slice(0, -1);
    //         setTimeout(deleteText, 50); // 调整速度
    //     } else {
    //         index = 0; // 重置索引以便重新开始
    //         setTimeout(typeWriter, 1000); // 删除完成后等待1秒开始重新显示
    //     }
    // }

    typeWriter(); // 开始打字效果
</script>   

## 一些思考

在一开始的时候，我只习惯于手写笔记或者在平板上记笔记，没有作电子笔记的想法。只是后来偶然看到朋友分享的电子笔记，于是一个念头就萌发出来：为什么不试试做电子笔记呢？我第一份完完整整的电子笔记是概统的笔记，非常感谢吴国桢老师的清晰明了的PPT，让我成功作出这份有大约一万字的笔记。

这个是整理电子笔记的开始。

从大二下开始，我开始做更多的电子笔记，从刚开始只用一个 md 文件，到新建一个文件夹分内容存放，我一直在摸索该做什么样的笔记，又该如何做笔记。但是很多时候，我所谓的笔记只是将课本的东西移到笔记中，没有老师的讲解，也没有自己的思考，甚至很多时候还忽略很多过程，显得割裂。我最满意的笔记是振动力学的笔记，因为框架很清楚，也不杂乱。

所以很多时候我开始怀疑做这些事情的目的，我是不是只是为学习这些知识找一个借口？我只需要将其从课本搬到网上就算是学过了，那做这些的意义是什么？学习怎么使用软件嘛？我是不是只是在模仿那位朋友的行为呢？

---

我没有总结的习惯，亦或者说我没有将一学期所学的东西好好整理并分享经验的念头，当时看到了很多很优秀的人做的总结贴，甚至还有很多汇总了很多优秀学校资源的网站，但是自己依旧是没有这个念头，也许是担忧自己的成绩并不优秀于是这些东西只是贻笑大方，也许只是我不太愿意去展示这些东西，但还是那个朋友，让我写下了第一份[总结贴](https://www.cc98.org/topic/5926541)。