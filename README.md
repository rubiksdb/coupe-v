- what is coupe-v

  coupe-v is a vector database with hierarchical graph (hnsw) backing the vector index.

  vector distance function are computed by one of euclidean/cosine/dot-product functions.

  smaller distance of two vectors means they are semantically close.

- tech stack

  other then hnsw index, the database is built on top of a distributed storage engine with
  n-way paxos replication.  User key/value/embedding/index are stored in b+tree by columns.

- tryout setup guide

  download coupe-preview-11-03-2024.tgz untar by

  ```
  tar xvf coupe-preview-11-03-2024.tgz
  cd wkk2
  ```

  start the program by script

  ```
  ./local/run-coupe.sh
  ```

  wait a second and you should see the following processes

  ```
  hxu@hxu-XPS-9320:~/test/wkk2$ ps -x | grep wkk
  43714 pts/0    Sl     0:02 /home/hxu/test/wkk2/local/../out/wkk-libra1 -s 127.0.0.1:10000 -d /home/hxu/var/ld1-1
  43715 pts/0    Sl     0:04 /home/hxu/test/wkk2/local/../out/wkk-coupe-be1 -s 127.0.0.1:10000 -l 127.0.0.1:10000 -l 127.0.0.1:20000 -l 127.0.0.1:30000 /home/hxu/var/dd1-1
  43716 pts/0    Sl     0:04 /home/hxu/test/wkk2/local/../out/wkk-coupe-be2 -s 127.0.0.1:20000 -l 127.0.0.1:10000 -l 127.0.0.1:20000 -l 127.0.0.1:30000 /home/hxu/var/dd2-1
  43717 pts/0    Sl     0:03 /home/hxu/test/wkk2/local/../out/wkk-coupe-be3 -s 127.0.0.1:30000 -l 127.0.0.1:10000 -l 127.0.0.1:20000 -l 127.0.0.1:30000 /home/hxu/var/dd3-1
  43903 pts/0    Sl     0:02 /home/hxu/test/wkk2/local/../out/wkk-coupe/wkk-coupe -s 127.0.0.1:10000 -l 127.0.0.1:10000 -l 127.0.0.1:20000 -l 127.0.0.1:30000
  ```

  create a table with command

  ```
  hxu@hxu-XPS-9320:~/test/wkk2$ go/bin/coupe-cli -s 127.0.0.1:10000 create -table foo -ndim 1536 -dfun dotproduct
    ok 1.247394ms
  ```

  table name foo, vector dimension 1536 and dot-product distance function are specified.

  let's import test data with command

  ```
  hxu@hxu-XPS-9320:~/test/wkk2$ go/bin/coupe-cli -s 127.0.0.1:10000 import -table foo -text local/text-data.txt -embedding local/embedding-data.txt
    ok 4.14800133s
  ```

  and now let's do ann (approximate nearest neighbor) query

  ```
  hxu@hxu-XPS-9320:~/test/wkk2$ go/bin/coupe-cli $S ann -table foo -text 标准化考试
    [0] 0.181 The SAT is a standardized test widely used for college admissions in the United States.
    [1] 0.203 For much of its history, it was called the Scholastic Aptitude Test and had two components, Verbal and Mathematical, each of which was scored on a range from 200 to 800.
    [2] 0.209 Since 2007, all four-year colleges and universities in the United States that require a test as part of an application for admission will accept either the SAT or ACT, and as of Fall 2022, over 1400 four-year colleges and universities do not require any standardized test scores at all for admission, though some of them are applying this policy only temporarily due to the coronavirus pandemic.
    [3] 0.215 Although taking the SAT, or its competitor the ACT, is required for freshman entry to many colleges and universities in the United States,[30] during the late 2010s, many institutions made these entrance exams optional,[31][32][33] but this did not stop the students from attempting to achieve high scores[34] as they and their parents are skeptical of what optional means in this context.
    [4] 0.216 New tools such as a question flagger, a timer, and an integrated graphing calculator are included in the new test as well.
    [5] 0.217 The new test is adaptive, meaning that students have two modules per section (reading/writing and math), with the second module being adaptive to the demonstrated level based on the results from the first module.
    [6] 0.219 Later it was called the Scholastic Assessment Test, then the SAT I: Reasoning Test, then the SAT Reasoning Test, then simply the SAT.
    [7] 0.219 Later it was called the Scholastic Assessment Test, then the SAT I: Reasoning Test, then the SAT Reasoning Test, then simply the SAT.
    ok 557.703165ms
  ```

  be advised that we have 3 topics in the test dataset: SAT test, CNN and nearest neighbor.

  environment variable OPENAI_API_KEY is need for ann query.  we use openai api for embedding generation.  
