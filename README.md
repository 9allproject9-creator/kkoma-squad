# 꼬마 부대 대작전

초등학생용 3D 러너 슈팅 게임입니다. `index.html` 파일 하나로 실행되며, 빌드 과정이 없습니다.

## 반 전체 랭킹 (Supabase)

1. https://supabase.com 에서 무료 프로젝트를 만듭니다.
2. **SQL Editor**에서 아래 SQL을 실행합니다.
3. `index.html`의 `SUPABASE_URL`, `SUPABASE_ANON_KEY` 두 값을 채웁니다.
   (Project Settings → API의 Project URL과 anon public key. service_role 키는 절대 넣지 마세요.)

```sql
create table ranking (
  name text primary key check (char_length(name) between 1 and 8),
  stage int not null check (stage between 1 and 999),
  score int not null check (score >= 0),
  date bigint not null
);
alter table ranking enable row level security;
create policy "누구나 랭킹 보기" on ranking for select using (true);

create or replace function submit_score(p_name text, p_stage int, p_score int)
returns boolean language plpgsql security definer set search_path = public as $$
declare cur ranking;
begin
  if char_length(p_name) not between 1 and 8 or p_stage not between 1 and 999 or p_score < 0 then
    return false;
  end if;
  select * into cur from ranking where name = p_name;
  if found and (cur.stage > p_stage or (cur.stage = p_stage and cur.score >= p_score)) then
    return false;
  end if;
  insert into ranking(name, stage, score, date)
  values (p_name, p_stage, p_score, (extract(epoch from now()) * 1000)::bigint)
  on conflict (name) do update set stage = excluded.stage, score = excluded.score, date = excluded.date;
  return true;
end $$;
grant execute on function submit_score(text, int, int) to anon;
```

두 값이 비어 있거나 인터넷이 끊기면 자동으로 그 기기 안의 랭킹을 사용합니다.

## 랭킹 기록 지우기

Supabase **Table Editor → ranking**에서 줄을 지우면 됩니다.
