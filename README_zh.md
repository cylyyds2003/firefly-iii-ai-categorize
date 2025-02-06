# Firefly III AI ����
����Ŀ������ͨ��ʹ�� OpenAI �Զ����� Firefly III �е�֧����

## ˵��ǰ��Ļ�
�����Ŀ�����ڹ����һ����������ֻ��һ��С�׶���û��̫���ʱ�䣬�������md�ļ��Ĵ��嶼��ֱ�ӷ�������ġ�����ϲ�����ֿ�Դ�ľ����𽥳��ԣ������һᾡ������ά�������Ŀ

��ӭ�������ǣ�

- ͨ�� Fork ��Ŀ��������

- ���� PR ������ Fork ������������ĵ�

- ��ͬά����Դ��̬

## ����ԭ��
���ṩ��һ�� webhook������������Ϊÿ�������֧��ʱ���á�

Ȼ������Ϊ OpenAI ����һ����ʾ�����������е�����տ��˺ͽ���������

OpenAI �����ݸ���ʾ�²⽻�׵����

����������е����֮һ���ù��߽����ý��׵����Ϊ�������һ����ǩ��

����޷���⵽���������������κ����ݡ�

��˽
��ע�⣬���׵�һЩ��ϸ��Ϣ����Ϊ��Ϣ���͸� OpenAI �Բ²����

��Щ��Ϣ������

��������
����Ŀ���˻�����
������������
��װ
1.  ��ȡ Firefly ���˷�������
�������ڸ�������ҳ���������Լ��ĸ��˷������ơ���¼������ Firefly III ʵ����ת����ѡ�>���������ϡ�>��OAuth�����ҵ������˷������ơ�����������������ơ�����һ���µĸ��˷������ơ�����һ����ʶ������Ʋ����������������˷������Ʒǳ�����ʹ�� Notepad++ �� Visual Studio Code �ȹ��߸���ճ������

![Step 1](docs/img/pat1.png)
![Step 2](docs/img/pat2.png)
![Step 3](docs/img/pat3.png)

2. ��ȡ OpenAI API ��Կ
����Ŀ��Ҫʹ������ OpenAI �˻�����Կ�������á�

ͨ������ OpenAI ��վ (https://platform.openai.com) ע��һ���˻�
�����˻��󣬷��� API ��Կҳ�� https://platform.openai.com/account/api-keys��
�������������Կ����ť����һ������Կ��
���� API ��Կ�������ܹ�������Կ��ʹ������

![OpenAI screenshot](docs/img/openai-key.png)

ע�⣺OpenAI Ŀǰ�ṩ 5 ��Ԫ����ѻ��֣���Ч��Ϊ 3 ���£���ǳ��ã���Ϊ�������ṩ������ϸ��Ϣ���ɿ�ʼ�� API �����״ν�����

֮������Ҫ���˻������üƷѡ�

��ʾ��ȷ������Ԥ�����ƣ��Է�ֹ�µ׳������⡣

3. ͨ�� Docker ����Ӧ�ó���
3.1 Docker Compose
����һ�����ļ� docker-compose.yml���������£�����ӵ����е� docker-compose �ļ��У���

```yaml
version: '3.3'

services:
  categorizer:
    image: ghcr.io/bahuma20/firefly-iii-ai-categorize:latest
    restart: always
    ports:
      - "3000:3000"
    environment:
      FIREFLY_URL: "https://firefly.example.com"
      FIREFLY_PERSONAL_TOKEN: "eyabc123..."
      OPENAI_API_KEY: "sk-abc123..."
```


ȷ����ȷ���û���������

���� `docker-compose up -d`��

����Ӧ�ó����������в���ͨ���˿� 3000 ���ʡ�

3.2 �ֶ�ͨ�� Docker
���д� Docker ����������Ӧ�ó����������༭����������ƥ��֮ǰ������ƾ�ݡ�

```shell
docker run -d \
-p 3000:3000 \
-e FIREFLY_URL=https://firefly.example.com \
-e FIREFLY_PERSONAL_TOKEN=eyabc123... \
-e OPENAI_API_KEY=sk-abc123... \
ghcr.io/bahuma20/firefly-iii-ai-categorize:latest
```


4. ���� webhook
��������������Ҫ�� Firefly ������ webhook���Ա�ÿ�����½��׽���ʱ�Զ��������ࡣ

��¼������ Firefly ʵ��
�ڲ������ת�����Զ�����>��Webhooks��
����������� webhook��
�� webhook һ�����⡣���硰AI ��������
����������������Ϊ�����״����󡱣�ӦΪĬ��ֵ��
������Ӧ������Ϊ���������顱��ӦΪĬ��ֵ��
�������ݡ�����Ϊ��JSON����ӦΪĬ��ֵ��
����URL������ΪӦ�ó���ɷ��ʵ� URL + ��/webhook�������磬�����ʹ�� docker-compose������ URL ����������ʾ��`http://categorizer:3000/webhook`
������ύ��

![Step 1](docs/img/webhook1.png)
![Step 2](docs/img/webhook2.png)
![Step 3](docs/img/webhook3.png)

��������׼��������ÿ�������׶�Ӧ�� OpenAI �Զ����ࡣ

�û�����
��Ӧ�ó������һ����С�� UI����������ط�����в��鿴 OpenAI ��ʾ����Ӧ��Ĭ������£��� UI �ǽ��õġ�

Ҫ���ô� UI���뽫�������� `ENABLE_UI` ����Ϊ `true`��

��������Ӧ�ó���󣬿���ͨ�� `http://localhost:3000/`�����κ��������������������� URL������ UI��

������ǩ����
Ӧ�ó�����Զ���ÿ���Ѵ����²�����Ľ��������ñ�ǩ��AI ���ࡱ��

������ͨ����Ӧ���û������� FIREFLY_TAG �����ô˱�ǩ�����ơ�

�ڲ�ͬ�˿�������
�������Ҫ��Ĭ�϶˿� `3000` ֮��Ķ˿�������Ӧ�ó��������û������� `PORT`��

�������������б�
`FIREFLY_URL`������ Firefly III ʵ���� URL�����磺`https://firefly.example.com`�������裩
`FIREFLY_PERSONAL_TOKEN`��Firefly III ���˷������ơ������裩
`OPENAI_API_KEY`��������֤ OpenAI �� OpenAI API ��Կ�������裩
`ENABLE_UI`���Ƿ�Ӧ�����û����档��Ĭ��ֵ��false��
`FIREFLY_TAG`��������Ѵ����׵ı�ǩ����Ĭ��ֵ��AI ���ࣩ
`PORT`��Ӧ�ó�������Ķ˿ڡ���Ĭ��ֵ��`3000`��