## **💡1. 프로젝트 개요**

**1-1. 프로젝트 소개**
- 프로젝트 명 : 생성형 AI 기반 얼굴 및 음성 생성 테스트 베드 구축
- 프로젝트 정의 : 이미지와 텍스트 입력만으로 감정 표현이 가능한 AI 페르소나를 생성하는 상호작용형 콘텐츠 시스템
  
  <img width="938" height="428" alt="image" src="https://github.com/HB674/25_HC236/blob/main/assets/GSDeep.png" /></br>

**1-2. 개발 배경 및 필요성**
- 짧은 영상 시대에도 특정 인물의 표정과 목소리를 자연스럽게 재현하는 일은 여전히 어려운 과제입니다. 생성형 AI는 한 장의 사진만으로 생생한 영상을 구현할 수 있어, 추모, 심리치료, 교육 등 다양한 분야에 활용 가능합니다. 이를 통해 정서적 위로와 몰입형 학습을 제공함으로써 사회적으로 긍정적인 영향을 끼칠 수 있습니다.

**1-3. 프로젝트 특장점**
- 텍스트와 이미지 입력만으로 음성 변조, 얼굴 애니메이션을 실시간 구현하는 멀티모달 AI 시스템
- TTS-Applio-SadTalker-Wav2Lip-gfpGAN 등 기술을 통합해 자연스러운 음성 변환과 인물 재현 가능
- 기존 솔루션을 통합한 자동화 End-to-End 파이프라인으로 간편한 서비스 지원 제공

**1-4. 주요 기능**
- Text-to-Speech : 텍스트 기반 음성 생성 제공
- Applio : 음성 데이터를 특정 화자의 목소리로 변환
- 딥페이크 : Sadtalker, Wav2lip, Gfpgan을 통해 정적 이미지에 생동감 있는 영상 생성
- 
- 멀티플랫폼 지원 : 웹·모바일 등 다양한 기기 환경에서 최적화된 서비스 제공

**1-5. 기대 효과 및 활용 분야**
- 기대 효과 :역사 인물 교육 등에서 정서적·교육적 가치 창출, 몰입도 높은 인터랙티브 콘텐츠 제공
- 활용 분야 : 추모, 교육, 엔터테인먼트, 1인 미디어 및 맞춤형 콘텐츠 제작 등 폭넓은 활용 가능

**1-6. 기술 스택**
- 프론트엔드 : React, Next.js, Tailwind CSS
- 백엔드 : Python(FastAPI), Node.js, Django
- AI/ML : PyTorch, TensorFlow, Hugging Face, OpenAI API
- 데이터베이스 : PostgreSQL, MongoDB, Elasticsearch
- 클라우드 : AWS
- 배포 및 관리 : Docker, Kubernetes, GitHub Actions

---

## **💡2. 팀원 소개**
| <img width="80" height="100" src="https://github.com/user-attachments/assets/ab73bb1c-c1d4-464d-8ad3-635b45d5a8ae" > | <img width="80" height="100" alt="image" src="https://github.com/user-attachments/assets/c7f66b7c-ab84-41fa-8fba-b49dba28b677" > | <img width="80" height="100" alt="image" src="https://github.com/user-attachments/assets/c33252c7-3bf6-43cf-beaa-a9e2d9bd090b" > | <img width="80" height="100" alt="image" src="https://github.com/user-attachments/assets/0d5909f0-fc73-4ab9-be09-4d48e3e71083" > | <img width="80" height="100" alt="image" src="https://github.com/user-attachments/assets/c7f66b7c-ab84-41fa-8fba-b49dba28b677" > |
|:---:|:---:|:---:|:---:|:---:|
| **김호원** | **임은택** | **전한범** | **최준우** | **권용원** |
| • 프론트엔드 <br> • 딥페이크 | • 딥보이스 <br> • 영상 제작 | • 백엔드 <br> • API 개발 |• 팀장 <br> • 딥페이크 | • 프로젝트 멘토 <br> • 기술 자문 |




---
## **💡3. 시스템 구성도**
> **(참고)** S/W구성도, H/W구성도, 서비스 흐름도 등을 작성합니다. 시스템의 동작 과정 등을 추가할 수도 있습니다.
- 서비스 흐름도
<img width="959" height="576" alt="image" src="https://github.com/HB674/25_HC236/blob/main/assets/sevice.png" />



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
