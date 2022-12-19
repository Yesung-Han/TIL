1. Create Table

    1. Aurora DB 에서 타깃 테이블 DDL만 추출
    2. snowflake용 DDL로 변환 (이 부분 수동으로 했는데 자동화 하고 싶다~!!!!)
    3. snowflake에서 테이블 생성


2. Aurora DB→ S3(csv)
SELECT INTO OUTFILE S3 쿼리 활용
-> AWS IAM 에서 권한 설정 및 RDB 권한 파라미터 설정 해주어야 함.

3. S3(csv) → snowflake
snowflake에서 for문 쿼리를 지원하기 때문에 external stage 경로를 순회하면서 copy into 작업 수행

      ```
      DECLARE
          tn varchar;
          query text;
          c1 CURSOR FOR SELECT table_name from rds_table_names;
      BEGIN
          FOR record in c1 DO
              tn := record.table_name;     
              query := 'copy into ' || tn  || ' from @S3_EXT_RDS/log/' || tn || ' file_format = (format_name = csv_format_rds) ON_ERROR = CONTINUE;';
          
              EXECUTE IMMEDIATE :query; -- will adapt this to be an update statement eventually. 
          END FOR;
      END;
      ```