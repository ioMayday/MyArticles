
**Ŀ¼**
[toc]

���⣺6��ָ���ͨGitȫ���̣�С��Ҳ�ܿ����ĳ����̳�

## Զ�̽��֣�������ȡ����
----

```bash
git clone https��ַ��ssh��ַ
git checkout branch_name ## ��Զ�˷�֧branch_nanmeͬ��������ͬ����֧��
```

## �����ύ
----

```bash
git pull origin               // ��Զ�˲ֿ�ͬ������
git add .                     // ���޸��ύ��������
git commit -m "..."           // �ύ�����زֿ�洢�������԰�������; -m��ʾcommit���и�����message��Ϣ����"..."��
git push private branch_name  // �Ƶ�Զ�˲ֿ��branch��֧
```

**����**������6��ָ������Git����ȫ�����������������ճ������������������������ʱ���ٽ�һ�����о��˹��߾����ˡ�

**��ʾ**����ָ��git review�޷��ύ��**�滻git review** ��ֱ����ҳforkһ�����ϴ�������˽�֣�Ȼ������ҳ����ͨ��˽�ֺϲ������ɣ�����ҳ����MergeRequest�����reviewer���ɡ�

```bash
git remote add private https://......git
git remote -v
git remote add private ssh:/.......git
git status
git commit --amend   ## ǿ���޸�֮ǰ���commit˵��
git push -f private branch_name 
```
�ϴ�ǰ���� `git status` ȷ�����������������粻�����µ�Ҫ�� `git pull origin` ͬ�����´�������ύ��

## �汾����
----

- ��ҳ�Ϲر�**merge request**����
- **soft**�����˵�ĳ���汾��������ǰ���Ǹ��汾��û��ɾ����
 ָ�  `git reset --soft HEAD^`
- ��**hard**ǿ��ɾ����������ɾ��֮ǰ���ع����������޸ļ��ύ���ݡ�
 ָ� `git reset --hard HEAD^`
- ���������һ���ύ���̣�ָ�����£�
   `git add .  `
   `git commit "˵��"`
   `git push -f ...   #ע��Ҫ���-f����ʾǿ��ͬ����Զ��`


## ��������
----

 - **һ��㶨���޸�Զ�ֿ̲��ַ**

```bash
git remote -v  ## �鿴origin��ԭԶ�˲ֿ��ַ
git remote set-url origin https://192.168.100.235:9797/john/git_test.git  #����Զ�˲ֿ��µ�ַ
```

## ��չ�ο�
----
1. [GitHub���ּ�Զ��ͬ������ಽ���ܽ�](https://blog.csdn.net/qq_17256689/article/details/112505349)
2. [Git�̳� | ����̳�](https://www.runoob.com/git/git-tutorial.html)
3. [���ף�GitԶ�ֿ̲��ַ�����������޸�](https://blog.csdn.net/asdfsfsdgdfgh/article/details/54981823)
4. [���ף�git���ذ汾������Զ�˰汾����(�ع�)](https://blog.csdn.net/tsq292978891/article/details/78965693)
