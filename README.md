## **💡1. 프로젝트 개요**

**1-1. 프로젝트 소개**
- 프로젝트 명 : 생성형 AI 기반 얼굴 및 음성 생성 테스트 베드 구축
- 프로젝트 정의 : 사용자의 검색 의도를 이해하고 최적의 정보를 제공하는 AI 기반 맞춤형 검색 서비스
  <img width="400" height="400" alt="image" src="https://github.com/HB674/25_HC236/blob/main/Frontend/assets/img/navigation-logo.png" /></br>

**1-2. 개발 배경 및 필요성**
- 현대 사회는 방대한 정보가 실시간으로 생성되고 축적되고 있습니다. 그러나 사용자가 원하는 정확한 정보를 찾기 위해서는 여전히 많은 시간과 노력이 필요합니다. 기존 키워드 기반 검색 방식은 사용자의 맥락이나 의도를 충분히 반영하지 못해 효율성이 떨어집니다. 따라서 사용자의 검색 목적을 인공지능이 이해하고 개인화된 결과를 제공하는 맞춤형 검색 서비스가 필요합니다.

**1-3. 프로젝트 특장점**
- 사용자의 검색 의도와 맥락을 이해하는 자연어 이해 기반 검색 서비스
- 단순 키워드 매칭이 아닌 의미 기반 정보 추천 및 순위화
- 개인별 기록과 관심사를 반영한 맞춤형 검색 결과 제공
- 다양한 데이터 소스를 연동해 멀티도메인 활용 가능성 확보
- 최신 AI/ML 프레임워크와 대규모 언어모델(LLM)을 활용한 최적화된 사용자 경험

**1-4. 주요 기능**
- AI 맞춤 검색 서비스 : 검색 의도를 분석하고 개인화된 결과 제공
- 자연어 질의 처리 : 키워드뿐 아니라 문장 단위 질문도 이해 가능
- 의미 기반 추천 : 단순한 ‘정확 단어 일치’가 아닌 맥락과 의미를 기반으로 한 결과 제공
- 맞춤형 필터링 및 정렬 : 사용자 성향에 따라 검색 결과 필터 및 순위 조정
- 멀티플랫폼 지원 : 웹·모바일 등 다양한 기기 환경에서 최적화된 검색 경험 제공

**1-5. 기대 효과 및 활용 분야**
- 기대 효과 : 검색 품질 향상 및 정보 탐색 효율 극대화, 다양한 산업 분야에서 데이터 활용성 확대
- 활용 분야 : 학술·연구, 커머스·쇼핑, 헬스케어, 뉴스·미디어, 기업 내부 문서 검색 등

**1-6. 기술 스택**
- 프론트엔드 : React, Next.js, Tailwind CSS
- 백엔드 : Python(FastAPI), Node.js, Django
- AI/ML : PyTorch, TensorFlow, Hugging Face, OpenAI API
- 데이터베이스 : PostgreSQL, MongoDB, Elasticsearch
- 클라우드 : AWS
- 배포 및 관리 : Docker, Kubernetes, GitHub Actions

---

## **💡2. 팀원 소개**
|:---:|:---:|:---:|:---:|:---:|
| **홍길동** | **한이음** | **최지수** | **이철수** | **김멘토** |
| • 개발총괄 <br> • UI/UX 기획 | • 백엔드 <br> • 프론트엔드 | • API 개발 <br> • DB 서버 구축 |• 데이터 분석 <br> • 전처리 | • 프로젝트 멘토 <br> • 기술 자문 |



---
## **💡3. 시스템 구성도**
> **(참고)** S/W구성도, H/W구성도, 서비스 흐름도 등을 작성합니다. 시스템의 동작 과정 등을 추가할 수도 있습니다.
- 서비스 구성도
<img width="500" height="500" alt="image" src="https://github.com/user-attachments/assets/28fc8453-d1a0-4184-8fd0-130d93d18545" />


- 엔티티 관계도
<img width="500" height="500" alt="image" src="https://github.com/user-attachments/assets/76e3347b-6d94-491e-8aeb-a7b4601c54d5" />


---
## **💡4. 작품 소개영상**

[![한이음 드림업 프로젝트 소개](https://github.com/HB674/25_HC236/blob/main/assets/youtubeimage.jpg)](youtube.com/watch?v=X-O7OI657Z0&feature=youtu.be)


---
## **💡5. 핵심 소스코드**
- 소스코드 설명 : API를 활용해서 자동 배포를 생성하는 메서드입니다.

```Python
async def create_tts_audio_job(req: TTSJobRequest):
    # 0) 이미지 확보: 주어지면 검증, 없으면 최신 자동
    if req.image_path and req.image_path.strip():
        image_in = _as_shared_path(req.image_path)
        if not image_in.exists():
            raise HTTPException(status_code=404, detail=f"image not found: {image_in}")
        image_auto = False
    else:
        latest_img = _pick_latest_image()
        if not latest_img:
            raise HTTPException(status_code=404, detail="no image found under input_image/")
        image_in = latest_img
        image_auto = True
    
    # chosen_voice = req.voice or _tts_voice_from_profile(req.voice_profile, fallback="onyx")
    if req.voice_profile:
        chosen_voice = _tts_voice_from_profile(req.voice_profile, fallback="onyx")
    else:
        chosen_voice = req.voice or "onyx"

    # 1) OpenAITTS 호출 준비
    form = {
        "voice": chosen_voice,
        "response_format": (req.response_format or "mp3"),
        "auto_ssml_wrap": "true" if (req.auto_ssml_wrap is None or req.auto_ssml_wrap) else "false",
    }
    text_source = "inline"
    if req.tts_text is not None and req.tts_text.strip():
        form["text"] = req.tts_text
    else:
        text_source = "latest_file"
    if req.output_basename:
        form["output_basename"] = req.output_basename

    # 2) 합성 실행 + 시간 측정
    async with httpx.AsyncClient(timeout=None) as client:
        t0 = time.perf_counter()
        r = await client.post(OPENAITTS_SYN_URL, data=form)
        ms = int((time.perf_counter() - t0) * 1000)
        if r.status_code != 200:
            raise HTTPException(status_code=502, detail=f"openaitts http_{r.status_code}: {r.text[:300]}")
        jr = r.json()
        rel = jr.get("relative")
        out = jr.get("output")
        if not rel and out:
            try:
                rel = str(Path(out).resolve().relative_to(SHARED_DIR.resolve()))
            except Exception:
                rel = out
        if not rel:
            raise HTTPException(status_code=500, detail=f"openaitts response missing path: {jr}")

    mapped_pitch = VOICE_PROFILE_TO_PITCH.get(req.voice_profile, 0)

    # 3) 기존 /jobs/audio와 동일한 요청으로 변환
    audio_req = AudioJobRequest(
        audio_path=rel,
        image_path=str(image_in.resolve().relative_to(SHARED_DIR.resolve())),
        use_applio=req.use_applio,
        pitch=mapped_pitch,
        voice_profile=req.voice_profile,
        pth_path=req.pth_path,
        index_path=req.index_path,
    )

    # 4) 잡 생성 + TTS 메타/타이밍/아티팩트 선기록
    job = _new_job_state(audio_req)
    job.params["tts_mapped_pitch"] = mapped_pitch
    job.params["tts"] = {
        "voice": chosen_voice,
        "response_format": req.response_format or "mp3",
        "auto_ssml_wrap": bool(req.auto_ssml_wrap if req.auto_ssml_wrap is not None else True),
        "text_source": text_source,  # "inline" | "latest_file"
        "output_basename": req.output_basename,
    }
    job.params["image_auto_selected"] = image_auto
    job.artifacts["tts_audio"] = rel                 # 상대경로
    job.artifacts["tts_audio_abs"] = out or None     # 절대경로(있으면)
    job.timings["openaitts"] = ms

    JOBS[job.job_id] = job
    asyncio.create_task(_run_audio_job(job))
    return {"job_id": job.job_id, "status": job.status}
