# TechPantheon ICS Community Repository

欢迎来到我们的社群的 GitHub 仓库！这个仓库目前是为了促进我们团队成员在期末考试期间的合作学习和复习而创建的。

## 现阶段任务

我们的任务是集中精力进行期末考试的复习，并以每门课程为单位，按照个人的进度进行笔记整理。课程如下：

### Y2S1：
1. **CPT101 - Computer Systems**
2. **CPT103 - Introduction to Databases**
3. **CPT107 - Discrete Mathematics and Statistics**
4. **CPT111 - Java Programming**

### Y2S2：
1. **CPT102 - Data Structures**
2. **CPT104 - Operating Systems Concepts**
3. **INT102 - Algorithmic Foundations and Problem Solving**
4. **INT104 - Artificial Intelligence**

每位成员将按照个人的复习进度，对相应的课程进行笔记整理。我们鼓励大家注重深度理解和掌握知识，以便最终能够为整个社群提供高质量的综合学习材料。

## 目标

我们的目标是建立一个积极、协作的学习社群，通过每个人的努力，为大家提供最好的复习资源。具体来说，我们的目标包括：

- **深度理解**：每位成员通过个人复习和笔记整理，达到对课程内容的深度理解。
- **协作整合**：将每门课程的个人笔记整合到一个综合的学习材料中，以便为整个社群提供完整的学习资源。
- **互助合作**：通过讨论、解答疑问和相互支持，确保每个成员都能够取得最好的学习效果。

## 文件目录（目前）

```markdown
|------ CPT/INT
    |------ 课程编号
        |
        |----- Person1/
        |----- Person2/
        |----- Person3/
        |----- Person4/
        |----- Person5/
```

## 如何贡献

欢迎每位成员积极参与社群活动！如果你想要贡献或提出建议，请按照以下步骤操作：

1. **复习笔记**：按照自己的进度整理每门课程的复习笔记。
2. **Pull Request**：将你的笔记提交为一个 Pull Request，以便其他人可以查看和提出建议。
3. **讨论**：在 Issues 中分享你的疑问、建议或者想法，与社群成员一起讨论。

## 推送规范

在开始贡献之前，请确保你的本地主分支与远程主分支同步。你可以使用以下命令：

```bash
git pull origin main
```

创建一个新的分支，分支名应该采用以下格式：

```bash
课目编号-名字
```

例如，如果你是在 CPT101 课程中，名字是 Person1，那么你的分支名可以是：

```bash
CPT101-Person1
```

进入你所在课程的目录，推送你产出的文件，确保你的推送消息（commit message）清晰地描述了你的更改，具体参考提交规范（自己百度）。

```bash
git add .
git commit -m "feat: 添加 CPT101 笔记"
git push origin CPT101-Person1
```

创建一个 Pull Request，标题和描述应该清晰、简明扼要。确保你的分支与主分支同步，没有冲突，并经过其他团队成员的审核。

如果你的 Pull Request 通过审查，并且没有冲突，由项目维护者进行合并。

重复以上步骤，确保每位成员的笔记都能够被整合到一个综合的学习材料中。











