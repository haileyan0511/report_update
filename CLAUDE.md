앞으로 진행될 대화의 컨텍스트를 위해 내 데이터베이스 구조와 프로젝트 아키텍처를 기억해 줘. 

[데이터베이스 스키마 정보]
- 데이터베이스 명: depart_data
- 테이블 간 관계 구조 (DDL):
-- public._prisma_migrations definition

-- Drop table

-- DROP TABLE public._prisma_migrations;

CREATE TABLE public._prisma_migrations (
	id varchar(36) NOT NULL,
	checksum varchar(64) NOT NULL,
	finished_at timestamptz NULL,
	migration_name varchar(255) NOT NULL,
	logs text NULL,
	rolled_back_at timestamptz NULL,
	started_at timestamptz DEFAULT now() NOT NULL,
	applied_steps_count int4 DEFAULT 0 NOT NULL,
	CONSTRAINT _prisma_migrations_pkey PRIMARY KEY (id)
);


-- public.client_sprint_notes definition

-- Drop table

-- DROP TABLE public.client_sprint_notes;

CREATE TABLE public.client_sprint_notes (
	id bigserial NOT NULL,
	client_id int8 NOT NULL,
	sprint_number int4 NOT NULL,
	title text NULL,
	focus text NULL,
	objectives jsonb NULL,
	notes text NULL,
	tags _text NULL,
	created_at timestamptz DEFAULT now() NOT NULL,
	updated_at timestamptz DEFAULT now() NOT NULL,
	CONSTRAINT client_sprint_notes_pkey PRIMARY KEY (id)
);


-- public.clients definition

-- Drop table

-- DROP TABLE public.clients;

CREATE TABLE public.clients (
	id bigserial NOT NULL,
	username text NOT NULL,
	"password" text NOT NULL,
	email text NULL,
	is_admin bool DEFAULT false NULL,
	is_active bool DEFAULT true NOT NULL,
	last_login_at timestamptz DEFAULT now() NULL,
	created_at timestamptz DEFAULT now() NOT NULL,
	updated_at timestamptz DEFAULT now() NOT NULL,
	depart_brand_id int8 NULL,
	CONSTRAINT clients_pkey PRIMARY KEY (id)
);
CREATE UNIQUE INDEX idx_clients_depart_brand_id ON public.clients USING btree (depart_brand_id);


-- public.business_portfolios definition

-- Drop table

-- DROP TABLE public.business_portfolios;

CREATE TABLE public.business_portfolios (
	id bigserial NOT NULL,
	client_id int8 NULL,
	fb_business_id varchar(64) NOT NULL,
	business_name text NULL,
	created_at timestamptz DEFAULT now() NOT NULL,
	updated_at timestamptz DEFAULT now() NOT NULL,
	CONSTRAINT business_portfolios_pkey PRIMARY KEY (id),
	CONSTRAINT business_portfolios_client_id_fkey FOREIGN KEY (client_id) REFERENCES public.clients(id)
);


-- public.client_info definition

-- Drop table

-- DROP TABLE public.client_info;

CREATE TABLE public.client_info (
	client_id int8 NOT NULL,
	brand_name _text NULL,
	init_essential _text NULL,
	CONSTRAINT client_info_pkey PRIMARY KEY (client_id),
	CONSTRAINT client_info_client_id_fkey FOREIGN KEY (client_id) REFERENCES public.clients(id)
);


-- public.client_members definition

-- Drop table

-- DROP TABLE public.client_members;

CREATE TABLE public.client_members (
	id bigserial NOT NULL,
	client_id int8 NULL,
	"role" varchar(64) NOT NULL,
	sub_role varchar(64) NULL,
	"name" text NULL,
	created_at timestamptz DEFAULT now() NOT NULL,
	updated_at timestamptz DEFAULT now() NOT NULL,
	CONSTRAINT client_members_pkey PRIMARY KEY (id),
	CONSTRAINT client_members_client_id_fkey FOREIGN KEY (client_id) REFERENCES public.clients(id)
);


-- public.ig_accounts definition

-- Drop table

-- DROP TABLE public.ig_accounts;

CREATE TABLE public.ig_accounts (
	id bigserial NOT NULL,
	business_portfolio_id int8 NOT NULL,
	fb_ig_id varchar(64) NOT NULL,
	username text NULL,
	is_active bool DEFAULT true NOT NULL,
	connected_at timestamptz NULL,
	disconnected_at timestamptz NULL,
	created_at timestamptz DEFAULT now() NOT NULL,
	updated_at timestamptz DEFAULT now() NOT NULL,
	CONSTRAINT ig_accounts_pkey PRIMARY KEY (id),
	CONSTRAINT ig_accounts_business_portfolio_id_fkey FOREIGN KEY (business_portfolio_id) REFERENCES public.business_portfolios(id)
);


-- public.ig_contents definition

-- Drop table

-- DROP TABLE public.ig_contents;

CREATE TABLE public.ig_contents (
	id bigserial NOT NULL,
	ig_id int8 NOT NULL,
	fb_ig_media_id varchar(64) NOT NULL,
	caption text NULL,
	ig_media_type text NOT NULL,
	ig_permalink text NULL,
	ig_timestamp timestamptz NOT NULL,
	media_url text NULL,
	thumbnail_url text NULL,
	created_at timestamptz DEFAULT now() NOT NULL,
	updated_at timestamptz DEFAULT now() NOT NULL,
	CONSTRAINT ig_contents_pkey PRIMARY KEY (id),
	CONSTRAINT ig_contents_ig_id_fkey FOREIGN KEY (ig_id) REFERENCES public.ig_accounts(id)
);


-- public.ig_insights_demographics definition

-- Drop table

-- DROP TABLE public.ig_insights_demographics;

CREATE TABLE public.ig_insights_demographics (
	ig_id int8 NOT NULL,
	age_range varchar(50) NOT NULL,
	gender varchar(50) NOT NULL,
	as_of_date date NOT NULL,
	followers int4 NULL,
	engaged_audience int4 NULL,
	created_at timestamptz DEFAULT now() NOT NULL,
	updated_at timestamptz DEFAULT now() NOT NULL,
	CONSTRAINT ig_insights_demographics_pkey PRIMARY KEY (ig_id, age_range, gender, as_of_date),
	CONSTRAINT ig_insights_demographics_ig_id_fkey FOREIGN KEY (ig_id) REFERENCES public.ig_accounts(id)
);


-- public.ig_insights_total definition

-- Drop table

-- DROP TABLE public.ig_insights_total;

CREATE TABLE public.ig_insights_total (
	ig_id int8 NOT NULL,
	as_of_date date NOT NULL,
	total_reach int4 NULL,
	reach_ad int4 NULL,
	reach_post int4 NULL,
	reach_carousel_container int4 NULL,
	reach_carousel_item int4 NULL,
	reach_reel int4 NULL,
	reach_story int4 NULL,
	reach_follower int4 NULL,
	reach_non_follower int4 NULL,
	reach_follow_unknown int4 NULL,
	total_views int4 NULL,
	views_ad int4 NULL,
	views_post int4 NULL,
	views_carousel_container int4 NULL,
	views_carousel_item int4 NULL,
	views_reel int4 NULL,
	views_story int4 NULL,
	views_follower int4 NULL,
	views_non_follower int4 NULL,
	views_follow_unknown int4 NULL,
	followers_count int4 NULL,
	follows int4 NULL,
	unfollows int4 NULL,
	profile_views int4 NULL,
	total_interactions int4 NULL,
	likes int4 NULL,
	"comments" int4 NULL,
	shares int4 NULL,
	saves int4 NULL,
	replies int4 NULL,
	reposts int4 NULL,
	profile_links_taps int4 NULL,
	created_at timestamptz DEFAULT now() NOT NULL,
	updated_at timestamptz DEFAULT now() NOT NULL,
	CONSTRAINT ig_insights_total_pkey PRIMARY KEY (ig_id, as_of_date),
	CONSTRAINT ig_insights_total_ig_id_fkey FOREIGN KEY (ig_id) REFERENCES public.ig_accounts(id)
);


-- public.ig_organic_insights definition

-- Drop table

-- DROP TABLE public.ig_organic_insights;

CREATE TABLE public.ig_organic_insights (
	ig_id int8 NOT NULL,
	date_start date NOT NULL,
	date_end date NOT NULL,
	organic_views int4 NULL,
	created_at timestamptz DEFAULT now() NOT NULL,
	updated_at timestamptz DEFAULT now() NOT NULL,
	CONSTRAINT ig_organic_insights_pkey PRIMARY KEY (ig_id, date_start, date_end),
	CONSTRAINT ig_organic_insights_ig_id_fkey FOREIGN KEY (ig_id) REFERENCES public.ig_accounts(id)
);


-- public.ad_accounts definition

-- Drop table

-- DROP TABLE public.ad_accounts;

CREATE TABLE public.ad_accounts (
	id bigserial NOT NULL,
	business_portfolio_id int8 NOT NULL,
	ig_account_id int8 NULL,
	fb_ad_account_id varchar(64) NOT NULL,
	"name" text NULL,
	currency varchar(10) NULL,
	account_status int4 NULL,
	created_at timestamptz DEFAULT now() NOT NULL,
	updated_at timestamptz DEFAULT now() NOT NULL,
	CONSTRAINT ad_accounts_pkey PRIMARY KEY (id),
	CONSTRAINT ad_accounts_business_portfolio_id_fkey FOREIGN KEY (business_portfolio_id) REFERENCES public.business_portfolios(id),
	CONSTRAINT ad_accounts_ig_account_id_fkey FOREIGN KEY (ig_account_id) REFERENCES public.ig_accounts(id)
);


-- public.campaigns definition

-- Drop table

-- DROP TABLE public.campaigns;

CREATE TABLE public.campaigns (
	id bigserial NOT NULL,
	ad_account_id int8 NOT NULL,
	fb_campaign_id varchar(64) NOT NULL,
	"name" text NULL,
	objective varchar(64) NULL,
	status varchar(64) NULL,
	effective_status varchar(64) NULL,
	fb_created_time timestamptz NOT NULL,
	created_at timestamptz DEFAULT now() NOT NULL,
	updated_at timestamptz DEFAULT now() NOT NULL,
	CONSTRAINT campaigns_pkey PRIMARY KEY (id),
	CONSTRAINT campaigns_ad_account_id_fkey FOREIGN KEY (ad_account_id) REFERENCES public.ad_accounts(id)
);


-- public.ig_carousel_items definition

-- Drop table

-- DROP TABLE public.ig_carousel_items;

CREATE TABLE public.ig_carousel_items (
	id bigserial NOT NULL,
	content_id int8 NOT NULL,
	fb_child_media_id varchar(64) NOT NULL,
	sort_order int4 NOT NULL,
	child_media_type text NOT NULL,
	child_media_url text NULL,
	created_at timestamptz DEFAULT now() NOT NULL,
	updated_at timestamptz DEFAULT now() NOT NULL,
	CONSTRAINT ig_carousel_items_fb_child_media_id_key UNIQUE (fb_child_media_id),
	CONSTRAINT ig_carousel_items_pkey PRIMARY KEY (id),
	CONSTRAINT ig_carousel_items_content_id_fkey FOREIGN KEY (content_id) REFERENCES public.ig_contents(id)
);


-- public.ig_content_insights definition

-- Drop table

-- DROP TABLE public.ig_content_insights;

CREATE TABLE public.ig_content_insights (
	content_id int8 NOT NULL,
	as_of_date date NOT NULL,
	reach int4 NULL,
	likes int4 NULL,
	"comments" int4 NULL,
	shares int4 NULL,
	saved int4 NULL,
	total_interactions int4 NULL,
	"views" int4 NULL,
	follows int4 NULL,
	profile_visits int4 NULL,
	profile_activity int4 NULL,
	ig_reels_avg_watch_time int8 NULL,
	ig_reels_video_view_total_time int8 NULL,
	created_at timestamptz DEFAULT now() NOT NULL,
	updated_at timestamptz DEFAULT now() NOT NULL,
	CONSTRAINT ig_content_insights_pkey PRIMARY KEY (content_id, as_of_date),
	CONSTRAINT ig_content_insights_content_id_fkey FOREIGN KEY (content_id) REFERENCES public.ig_contents(id)
);


-- public.ad_sets definition

-- Drop table

-- DROP TABLE public.ad_sets;

CREATE TABLE public.ad_sets (
	id bigserial NOT NULL,
	campaign_id int8 NOT NULL,
	fb_ad_set_id varchar(64) NOT NULL,
	ad_set_name text NULL,
	optimization_goal varchar(64) NULL,
	billing_event varchar(64) NULL,
	status varchar(64) NULL,
	effective_status varchar(64) NULL,
	targeting_spec jsonb NULL,
	fb_created_time timestamptz NOT NULL,
	created_at timestamptz DEFAULT now() NOT NULL,
	updated_at timestamptz DEFAULT now() NOT NULL,
	CONSTRAINT ad_sets_pkey PRIMARY KEY (id),
	CONSTRAINT ad_sets_campaign_id_fkey FOREIGN KEY (campaign_id) REFERENCES public.campaigns(id)
);


-- public.ads definition

-- Drop table

-- DROP TABLE public.ads;

CREATE TABLE public.ads (
	id bigserial NOT NULL,
	ad_set_id int8 NOT NULL,
	account_id int8 NOT NULL,
	fb_ad_id varchar(64) NOT NULL,
	ad_name text NULL,
	body text NULL,
	status varchar(64) NULL,
	effective_status varchar(64) NULL,
	source_ig_media_id varchar(64) NULL,
	landing_page_url text NULL,
	thumb_link text NULL,
	fb_created_time timestamptz NOT NULL,
	created_at timestamptz DEFAULT now() NOT NULL,
	updated_at timestamptz DEFAULT now() NOT NULL,
	body_embedding public.vector NULL,
	CONSTRAINT ads_pkey PRIMARY KEY (id),
	CONSTRAINT ads_account_id_fkey FOREIGN KEY (account_id) REFERENCES public.ad_accounts(id),
	CONSTRAINT ads_ad_set_id_fkey FOREIGN KEY (ad_set_id) REFERENCES public.ad_sets(id)
);


-- public.ad_keywords definition

-- Drop table

-- DROP TABLE public.ad_keywords;

CREATE TABLE public.ad_keywords (
	ad_id int8 NOT NULL,
	essential_keywords _text NULL,
	variable_keywords _text NULL,
	updated_at timestamptz DEFAULT now() NOT NULL,
	CONSTRAINT ad_keywords_pkey PRIMARY KEY (ad_id),
	CONSTRAINT ad_keywords_ad_id_fkey FOREIGN KEY (ad_id) REFERENCES public.ads(id)
);


-- public.ad_performance_daily definition

-- Drop table

-- DROP TABLE public.ad_performance_daily;

CREATE TABLE public.ad_performance_daily (
	ad_id int8 NOT NULL,
	age_range varchar(50) NOT NULL,
	gender varchar(50) NOT NULL,
	as_of_date date NOT NULL,
	reach int4 NULL,
	impressions int4 NULL,
	clicks int4 NULL,
	ctr float8 NULL,
	frequency float8 NULL,
	spend float8 NULL,
	purchase_count int4 NULL,
	purchase_value float8 NULL,
	purchase_roas float8 NULL,
	goal_conv_count int4 NULL,
	goal_conv_value float8 NULL,
	goal_conv_cpa float8 NULL,
	goal_conv_name text NULL,
	goal_conv_id varchar(64) NULL,
	cpc float8 NULL,
	cpm float8 NULL,
	link_clicks int4 NULL,
	view_content int4 NULL,
	add_to_cart int4 NULL,
	initiate_checkout int4 NULL,
	complete_registration int4 NULL,
	instagram_profile_visits int4 NULL,
	website_landing_page_views int4 NULL,
	inline_post_engagement int4 NULL,
	post_reactions int4 NULL,
	"comments" int4 NULL,
	post_saves int4 NULL,
	video_views int4 NULL,
	video_p25_watched int4 NULL,
	video_p50_watched int4 NULL,
	video_p75_watched int4 NULL,
	video_p100_watched int4 NULL,
	video_thruplay_watched int4 NULL,
	created_at timestamptz DEFAULT now() NOT NULL,
	updated_at timestamptz DEFAULT now() NOT NULL,
	CONSTRAINT ad_performance_daily_pkey PRIMARY KEY (ad_id, age_range, gender, as_of_date),
	CONSTRAINT ad_performance_daily_ad_id_fkey FOREIGN KEY (ad_id) REFERENCES public.ads(id)
);

[프로젝트 파이프라인 아키텍처]
- 목적: 보고서 자동화
- 흐름:
데이터 추출 (processor.py)
JSON 저장 (to_json.py)
차트 생성 (visualizer_test.py)
렌더링 (main.py, template.html)

[하네스 실행 및 보안 지침 (Harness Rules)]
당신은 이 프로젝트의 자동화 보고서 파이프라인을 구축하고 유지보수하는 AI 에이전트입니다. 아래 규칙을 엄격히 준수하십시오.

1. 보안 및 권한 통제
- 지정된 작업 디렉토리 외부의 파일 시스템에 접근하거나 수정하지 마십시오.
- 데이터베이스 접근 시 반드시 SELECT 쿼리만 사용하십시오. 데이터베이스의 스키마나 데이터를 변경/삭제하는 명령어(DROP, UPDATE, DELETE 등)는 절대 실행하지 마십시오.
- API 키, 비밀번호, DB 접속 정보 등 민감한 정보는 절대 코드에 하드코딩하거나 콘솔/로그에 출력하지 마십시오. 환경 변수(.env)를 통해서만 참조하십시오.

2. 작업 수행 절차
- 앞으로 쿼리나 파이썬 코드를 작성할 때, 위의 파이프라인 흐름(특히 JSON 변환 및 차트 렌더링)에 가장 적합한 형태로 데이터를 가공하십시오.
- 결과를 반환할 때는 to_json.py가 예상하는 구조를 절대 훼손하지 마십시오.
- 불필요한 부연 설명을 생략하고, 실행 결과와 상태만 간결하게 보고하여 컨텍스트를 절약하십시오.

3. 최종 실행 및 오류 처리
- 요구된 코드 작성이 모두 완료되면, 반드시 python main.py를 실행하여 시스템을 구동하고 결과를 확인하십시오.
- 실행 중 오류가 발생할 경우, 에러 로그를 분석하여 원인을 파악하고 코드를 수정하여 재실행하십시오.
- 단, 자동 재시도는 최대 3회로 제한합니다. 3회 이상 실패 시 즉시 작업을 중단하고 에러 내역을 보고한 뒤 대기하십시오.

[초기 확인 지시사항]
이 내용을 완벽히 이해했다면 "depart_data 스키마 및 파이프라인, 그리고 하네스 실행 지침을 파악했습니다. 어떤 작업을 도와드릴까요?"라고 대답할 것.
