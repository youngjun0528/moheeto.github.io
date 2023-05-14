[---
title: "Pandas Optimization"
description:
published: true
date: 2023-05-03T15:00:00.000Z
tags:

- Data Analysis
- Pandas
  editor: markdown
  dateCreated: 2023-05-03T15:00:00.000Z
  categories:
- data_analysis
  author_profile: true
  sidebar_main: true
  toc: true
  toc_sticky: true
  toc_label: "Pandas Optimization"

---

## Python Memory Profiling

### memory-profiler

- https://github.com/fabianp/memory_profiler
- https://pypi.org/project/memory-profiler/
- line-by-line analysis of memory consumption for python programs
- 라인 단위로 메모리 사용량을 측정하는 라이브러리
- 사용 가이드
    - Python
        - 측정 하고자 하는 함수에 profile 데코레이터를 삽입한다.
        - 데코레이터가 설정된 Function의 Line By Line 으로 메모리 사용량이 표시된다.
        - 각 Line의 메모리 사용량은 <span style="color:red;font-weight:bold">최대 메모리 사용량</span>을 표시한다.
          ```python
          from memory_profiler import profile
      
          @profile
          def my_func():
            # Function Code
            pass
          my_func()
          ```
          ```
          # Result
          Line #    Mem usage    Increment  Occurrences   Line Contents
          =============================================================
               4    105.7 MiB    105.7 MiB           1   @profile
               5                                         def my_func():
               6   1975.3 MiB   1869.6 MiB           1       # Function Code
     
          ```
    - IPython Magic Command (Jupyter Notebook)
        - 각 Cell 안에 %%memit 를 삽입한다.
        - Cell 에서 사용한 <span style="color:red;font-weight:bold">최대 메모리 사용량</span>을 표시한다.
          ```jupyterpython
          import memory_profiler
          %load_ext memory_profiler
          ```
          ```jupyterpython
          %%memit
          # Function Code
          pass
          ```
          ```
          # Result
          peak memory: 2283.36 MiB, increment: 2159.20 MiB
          ```

### ipython-memory-usage

- https://github.com/ianozsvald/ipython_memory_usage
- https://pypi.org/project/ipython-memory-usage/
- 위에 설명한 memory-profiler 를 기반으로 IPython 에 최적화 된 Magic Command
- 사용 가이드
    - IPython Magic Command (Jupyter Notebook)
        - 각 Cell 안에 %%memit 를 삽입한다.
        - Cell 에서 사용한 <span style="color:red;font-weight:bold">마지막 메모리 사용량</span>을 표시한다.
          ```jupyterpython
          # 모니터링 시작
          import ipython_memory_usage.ipython_memory_usage
          %ipython_memory_usage_start
          ```
          ```jupyterpython
          # Function Code
          pass
          ```
          ```
          In [2] used 1.2422 MiB RAM in 0.15s, peaked 0.00 MiB above current, total RAM usage 1995.45 MiB
          ```
          ```jupyterpython
          # 모니터링 종료
          %ipython_memory_usage_stop
          ```

## 최적화 방법

### 최적화
- 테스트 데이터
    - https://www.kaggle.com/datasets/greegtitan/indonesia-wikipedia-pages?select=wikipedia_id-clean.csv
    - Size : 1.07GB
    - 카테고리 테스트를 위해서 Title 컬럼의 앞 글자만 별도로 추가함.
    - ![data-analysis-00](..%2F..%2Fassets%2Fimg%2Fdata_analysis%2Fdata-analysis-00.png)

#### [참고] Python 메모리 관리

https://github.com/python/cpython/blob/ad051cbce1360ad3055a048506c09bc2a5442474/Objects/obmalloc.c#L534
https://docs.python.org/ko/3.9/c-api/memory.html

- Python Memory Map ( Object-specific allocators )
    - Python 에서의 메모리 관리는 Python Memory 관리자에 의해서 내부적으로 처리된다.
    - 아래의 메모리 Map 의 우선순위에 의해서 Garbage Collector 가 실행된다.
    - https://towardsdatascience.com/python-garbage-collection-article-4a530b0992e3
        - 위 문서에 따르면 객체가 Garbage Collector에 의해 해제된 메모리는 새로운 Python 객체가 차지할 수 있으나,
        - 해제된 메모리는 시스템에 반환된다는 보장은 없다.
        - 최종적으로 C 라이브러리의 Malloc 함수를 통해서 메모리 관리가 이루어지기 때문에 추측된다.
        - 다만 Garbage Collector 를 강제로 수행 가능 하지만, Garbage Collector 수행 자체가 많은 비용(시간)이 드는 작업이므로 유의하여야 한다.
            - https://docs.python.org/ko/3.9/library/gc.html

<table>
    <tbody>
        <tr style="border: 2px solid;border-bottom: 1px;">
            <td rowspan=2> + 3 </td>
            <td>[ int ]</td>
            <td>[ dict ]</td>
            <td>[ list ]</td>
            <td> ... </td>
            <td>[ string ]</td>
            <td>Python core</td>
        </tr>
        <tr style="border: 2px solid;border-top: 1px;">
            <td colspan=5> <----- Object-specific memory -----> </td>
            <td><-- Non-object memory --></td>
        </tr>
        <tr style="border: 2px solid;border-bottom: 1px;">
            <td rowspan=2> + 2 </td>
            <td colspan=5>[ Python's object allocator ] </td>
            <td rowspan=2><------ Internal buffers ------></td>
        </tr>
        <tr style="border: 2px solid;border-top: 1px;">
            <td colspan=5> ####### Object memory ####### </td>
        </tr>
        <tr style="border: 2px solid;border-bottom: 1px;">
            <td rowspan=2> + 1 </td>
            <td colspan=6> [ Python's raw memory allocator (PyMem_ API) ] </td>
        </tr>
        <tr style="border: 2px solid;border-top: 1px;">
            <td colspan=6> <----- Python memory (under PyMem manager's control) ------> </td>
        </tr>
        <tr style="border: 2px solid;border-bottom: 1px;">
            <td rowspan=2> 0 </td>
            <td colspan=6> [ Underlying general-purpose allocator (ex: C library malloc) ] </td>
        </tr>
        <tr style="border: 2px solid;border-top: 1px;">
            <td colspan=6> <------ Virtual memory allocated for the python process -------> </td>
        </tr>
        <tr style="border: 2px solid;">
            <td colspan=6> ========================================================================= </td>
        </tr>
        <tr style="border: 2px solid;border-bottom: 1px;">
            <td rowspan=2> - 1 </td>
            <td colspan=6> [ OS-specific Virtual Memory Manager (VMM) ] </td>
        </tr>
        <tr style="border: 2px solid;border-top: 1px;">
            <td colspan=6> <--- Kernel dynamic storage allocation & management (page-based) ---> </td>
        </tr>
        <tr style="border: 2px solid;">
            <td rowspan=2> - 2 </td>
            <td colspan=4><-- Physical memory: ROM/RAM --> </td>
            <td colspan=3><-- Secondary storage (swap) --></td>
        </tr>
    </tbody>
</table>

#### Memory 최적화
- https://towardsdatascience.com/seven-killer-memory-optimization-techniques-every-pandas-user-should-know-64707348ab20
- https://towardsdatascience.com/how-to-handle-large-datasets-in-python-1f077a7e7ecf
##### 1) 단순 read_csv

- 아무런 옵션 없이 CSV 파일을 읽어들일 경우
    ```Python
    import pandas as pd
    data_df = pd.read_csv('data/wikipedia_id-clean.csv')
    ```
    - 분석
        - ![data-analysis-01](..%2F..%2Fassets%2Fimg%2Fdata_analysis%2Fdata-analysis-01.png)
        - peak memory: 2166.33 MiB, increment: 2104.30 MiB
        - used 1958.0547 MiB RAM in 13.64s, peaked 26.09 MiB above current, total RAM usage 2019.58 MiB
        - 메모리 사용 상승 추이를 살펴보면 500MB 까지는 선형으로 증가하다가 1GB 이후부터는 계단식 상승으로 이어지고 있다.
        - 모든 read 가 종료되면 1.7GB 수준으로 사용중인 메모리가 정리되면서 하락한다.

##### 2) CSV Chunk Size set read

- chunksize Option 추가
- 하나의 CSV 파일을 한꺼번에 Load 하는 것이 아닌, chunksize 만큼 반복하여 읽는다.
    ```Python
    import pandas as pd
    data_df = pd.DataFrame()
    for chunk in pd.read_csv('data/wikipedia_id-clean.csv', chunksize=10000):
        data_df = pd.concat([data_df, chunk], ignore_index=True)
    ```
    - 분석
        - ![data-analysis-02](..%2F..%2Fassets%2Fimg%2Fdata_analysis%2Fdata-analysis-02.png)
        - peak memory: 1996.92 MiB, increment: 1935.00 MiB
        - used 1935.5977 MiB RAM in 14.08s, peaked 0.00 MiB above current, total RAM usage 1996.98 MiB
        - 앞서 chunksize 옵션을 추가하면 메모리 상승 추이가 선형으로 이루어져 있고, Peak 메모리도 이전보다 200MB 가까이 감소한 것으로 알 수 있다.
        - pandas 에서 csv 파일을 읽을 때 chunksize 만큼 반복 수행하면서 주기적으로 메모리가 관리되는 것을 확인 가능하다.

##### 3) CSV Required Column read

- usecols Option 사용하여 분석에 필요한 컬럼만 읽어 들인다.
- 여기서는 id와 timestamp, text, check 정보만 읽어오도록 한다.
    ```Python
    import pandas as pd
    data_df = pd.DataFrame()
    usecols_list = ['id', 'timestamp', 'text', 'check']
    for chunk in pd.read_csv('data/wikipedia_id-clean.csv', chunksize=10000, usecols=usecols_list):
        data_df = pd.concat([data_df, chunk], ignore_index=True)
    ```
    - 분석
        - ![data-analysis-03](..%2F..%2Fassets%2Fimg%2Fdata_analysis%2Fdata-analysis-03.png)
        - peak memory: 1873.17 MiB, increment: 1811.40 MiB
        - used 1806.3242 MiB RAM in 13.60s, peaked 0.00 MiB above current, total RAM usage 1867.61 MiB
        - 기본 대비 300MB 정도 감소한다.
        - text 컬럼을 제외하면 더 적은 메모리로 데이터를 읽을 수 있다.

##### 4) CSV Column Data Type assign

- 데이터 타입을 추정하여 적절한 Data Type 을 지정하도록 한다.
    - Integer Number Type : int8, int16, int32, int64
    - Float Number Type : float16, float32 and float64.
    - Categorical Type (Data Unique Count 작을 경우)
    - Sparse data structures (NaN 이 많은 경우)
    - Pyarrow Data Type
- 이전 데이터의 정보를 보면 다음과 같다.
    ```python
    data_df.info()
    # RangeIndex: 638798 entries, 0 to 638797
    # Data columns (total 4 columns):
    #  #   Column     Non-Null Count   Dtype 
    # ---  ------     --------------   ----- 
    #  0   id         638798 non-null  int64 
    #  1   timestamp  638798 non-null  object
    #  2   text       638798 non-null  object
    #  3   check      638797 non-null  object
    # dtypes: int64(1), object(3)
    ```
    - int 형은 자동으로 int64 Dtype 으로 할당
    - string 형은 object Dtype 으로 할당된다. ( Object Type == String Type )
    - Pandas 의 Dtype 매핑 할당
        - id : int64
        - timestamp : datetime64[ns, UTC]
        - text : string[pyarrow]
            - 특히 String 의 경우 Object 가 아닌 Pyarrow Type 으로 지정한다. ( String Object 의 메모리 사용량 감소 )
                - https://pandas.pydata.org/docs/user_guide/pyarrow.html
                - https://pythonspeed.com/articles/pandas-string-dtype-memory/
        - check : category
            - 해당 데이터는 종류가 작을 경우 사용
            - 국가 코드, 도시 이름, A-Z 와 같은 알파벳 리스
        - NaN이 많지 않으므로 Sprase Data 구조는 사용하지 않는다. ( NaN 데이터를 압축해 주는 효과 )
            - https://pandas.pydata.org/docs/user_guide/sparse.html

    ```python
    import gc
    import pandas as pd
    from pandas.api.types import union_categoricals
      
    # 1) DataFrame 초기화시 각 컬럼 명과 타입을 미리 지정한다.
    data_df = pd.DataFrame({'id': pd.Series(dtype='int64'),
                            'timestamp': pd.Series(dtype='datetime64[ns, UTC]'),
                            'title': pd.Series(dtype='string[pyarrow]'),
                            'check': pd.Series(dtype='category')})
    # 2) CSV 파일을 읽을 때 필요한 각 컬럼 명과 타입을 지정한다.
    # - timestamp 의 경우 date_parser 를 추가하여 별도의 변환 작업이 필요하다.
    data_types = {'id':'int64',
                  'timestamp': 'string[pyarrow]',
                  'title':'string[pyarrow]',
                  'check':'category'}
    parse_dates = ['timestamp']
    date_format = '%Y-%m-%dT%H:%M:%SZ'
    for chunk in pd.read_csv('data/wikipedia_id-clean.csv', chunksize=10000, usecols=data_types.keys(), dtype=data_types, parse_dates=parse_dates, date_format=date_format):
        # 3) Convert Datetime
        chunk['timestamp'] = pd.to_datetime(chunk['timestamp'], utc=True)
        # 4) Category Dtype 의 Join
        uc = union_categoricals([data_df.check, chunk.check])
        chunk.check = pd.Categorical( chunk.check, categories=uc.categories )
        data_df.check = pd.Categorical( data_df.check, categories=uc.categories )
        # 4) chunk 데이터와 원본 데이터 프레임의 병합
        data_df = pd.concat([data_df, chunk], ignore_index=True)
    # 5) 사용이 완료된 chunk 데이터 프레임은 삭제
    del [[chunk]]
    gc.collect()
    ```
- 유의사항
    - Dataframe 의 column 과 Type 을 초기화 하지 않으면, concat 작업 결과는 타입을 예측 추정하여 Object type 으로 할당된다.
        - object + category = object OR object + string[pyarrow] = object
    - 날짜 형식의 컬럼 변환
        - pandas 2.0 이후 부터는 date_parser 사용이 deprecated 예정 ( 성능 문제로 인한 제거 )
        ```python
        date_format = '%Y-%m-%dT%H:%M:%SZ'
        date_parser = lambda x: pd.to_datetime(x, format=date_format, utc=True)
        for chunk in pd.read_csv('data/wikipedia_id-clean.csv', chunksize=10000, usecols=data_types.keys(), dtype=data_types, parse_dates=parse_dates, date_parser=date_parser):
            pass
        # FutureWarning: The argument 'date_parser' is deprecated and will be removed in a future version. Please use 'date_format' instead, or read your data in as 'object' dtype and then call 'to_datetime'.
        ```
        - date_format 을 사용하지 않는 경우 : 각 chunk 마다 to_datetime 변환 작업 필요
        ```python
        date_format = '%Y-%m-%dT%H:%M:%SZ'
        for chunk in pd.read_csv('data/wikipedia_id-clean.csv', chunksize=10000, usecols=data_types.keys(), dtype=data_types):
            chunk['timestamp'] = pd.to_datetime(chunk['timestamp'], format=date_format, utc=True)
        ```
    - 카테고리 형식의 Join 유의
        - 앞서 말한 바와 같이 Category Type 은 object + category = object 와 같이 변환이 된다.
        - [A,B] + [B,C] = [A,B,B,C] 가 아닌 [A,B] + [B,C] = [A,B,C] 로 Join 하고 싶다면 union_categoricals 를 사용한다.
    - 마지막 남은 chunk 데이터는 삭제하도록 한다.
- 분석
    - ![data-analysis-04](..%2F..%2Fassets%2Fimg%2Fdata_analysis%2Fdata-analysis-04.png)
    - peak memory: 1383.85 MiB, increment: 1322.18 MiB
    - used 1322.2930 MiB RAM in 17.55s, peaked 18.76 MiB above current, total RAM usage 1383.90 MiB
    - 기본 대비 800 MB 감소함.
    - 데이터 변환 작업은 Chunk Loop 안에서 처리하도록 한다. ( 작은 사이즈에서 변환 후 합치는 방법이 성능 면에서 좋다.)
      - 하지만 변환 작업에 소요되는 시간은 늘어난다.
    - string[pyarrow] 타입의 메모리 감소량은 상당하다.

##### 5) DataFrame Manipulation
- Pandas 에서 Dataframe을 조작하는 방법 중에서 아래 2가지 방법이 존재한다.
- 새로운 Dataframe 을 사용하지 않는 경우 Inplace Assignment로 조작을 권장한다.
  - Standard Assignment
    - 새로운 Dataframe을 Copy 하여 데이터를 조작하는 방법
    ```python
    df_copy = df.fillna(0)
    ```
  - Inplace Assignment
    - 기존의 Dataframe에 바로 데이터를 조작하는 방법
    ```python
    df.fillna(0, inplace = True)
    ```

##### 결론
- Pandas 의 메모리 최적화에 2가지 포인트는 다음과 같다.
  - 큰 파일은 작은 파일로 나누어서 읽는다.
  - String 문자열은 메모리 사용량에 가장 큰 영향을 미친다. 문자열은 적절한 Data Type 으로 변환 한다.
- 시스템 리소스의 자원 중 메모리와 CPU 성능, 시간과 비용 사이에서 적절한 타협점을 찾아야 한다.


#### Run-Time 최적화
- https://towardsdatascience.com/five-killer-optimization-techniques-every-pandas-user-should-know-266662bd1163
- https://towardsdatascience.com/why-i-stopped-dumping-dataframes-to-a-csv-and-why-you-should-too-c0954c410f8f
##### CSV File Read In/Out
- CSV File multiple time read : Convert Pickle or Feather or Parquet
    - 각 파일별 특징은 아래 URL 에서 확인 가능하다.
    - https://bawaji94.medium.com/feather-vs-parquet-vs-csv-vs-jay-55206a9a09b0
    - 가장 효율적인 것은 Feather 이지만, boolean 데이터에는 적합하지 않는 것 같다.
    - IO 성능은 Parquet 과 Feather 은 유사하지만, 
      - 다른 라이브러리와의 활용면에서 Parquet 형식이 더 우월한 것 같다. (Apache Spark 또는 Hadoop )
      - Boolean 데이터 형식의 경우 Parquet 가 더 우수하다.
      - Feather은 최초 장기 저장용 데이터로 설계되진 않고, 압축시 ZSTD 를 권장한다. ( Parquet 는 gzip )
    - 문제점
      - detype 에 대한 제어가 불가능하다. (특히 catogory 데이터)
      - dtype backend를 pyarrow를 사용하면 메모리 사용량이 낮아지지만 모든 데이터 타입이 pyarrow 형태로 바뀐다.
      - 기존보다 속도는 2개 빨라지고 메모리 사용량은 10% 정도 증가한다.
      - pyarrow 타입에 대한 연구가 필요함


- CSV File Only One read : Datatable Library
2. Data Filtering with Group by Categorical data
    ```Python
    ## Approach 1
    df1 = data[data["Company Name"] == "Amazon"]
    print("df1 type: ", type(df1))
    
    ## Approach 2
    df2 = data_grp.get_group("Amazon")
    print("df2 type: ", type(df2))
    ```
3. Dataframe Merge (Inner Join)
    ```Python
    ## Approach 1: pd.merge()
    pd.merge(df1, df2, on = "col_a", how = "inner")
    
    ## Approach 2: join()
    ## Make the merge column as the index.
    df1.set_index("col_a", inplace=True)
    df2.set_index("col_a", inplace=True)
    df1.join(df2)
    ```
4. Group By Count using value_counts()
    ```Python
    ## Approach 1
    data["Company Name"].value_counts()
   
    ## Approach 2
    data.groupby("Company Name").size()
    ```
5. DataFrame Iterating
    ```Python
    ## Approach 1
    for i in range(len(df)):
        salary_sum += df.iloc[i]['Employee Salary']
    ## Approach 2
    for index, row in df.iterrows():
        salary_sum += row['Employee Salary']
    ## Approach 3
    for row in df.itertuples(): 
        salary_sum += row._4
    ```
   
#### Pandas AWS S3 Access