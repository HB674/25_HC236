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
- Text-to-Speech : 텍스트 기반 음성 생성
- Applio : 음성 데이터를 특정 화자의 목소리로 변환 (Voice Conversion)
- 딥페이크 합성 : Sadtalker, Wav2lip, Gfpgan을 통해 정적 이미지에 생동감 있는 영상 생성
- FastAPI 백엔드 : 모듈 간 비동기 API 연동으로 콘텐츠 생성 흐름을 일괄 제어·스케일링
- 멀티플랫폼 지원 : 웹·모바일 등 다양한 기기 환경에서 최적화된 서비스 제공

**1-5. 기대 효과 및 활용 분야**
- 기대 효과 : 역사 인물 교육 등에서 정서적·교육적 가치 창출, 몰입도 높은 인터랙티브 콘텐츠 제공
- 활용 분야 : 추모, 교육, 엔터테인먼트, 1인 미디어 및 맞춤형 콘텐츠 제작 등 폭넓은 활용 가능

**1-6. 기술 스택**
- 프론트엔드 : HTML, CSS, JavaScript, jQuery
- 백엔드 : Python(FastAPI)
- AI/ML : PyTorch, OpenAI TTS, Applio(Voice Conversion), SadTalker, Wav2Lip, GFPGAN
- 클라우드 / 호스트•네트워킹 : Vercel, Cloudflare Tunnel
- 배포 및 운영 : Docker, Docker Compose
- 협업 / 문서화 : GitHub, Notion

---

## **💡2. 팀원 소개**
| <img width="173" height="228" src="https://github.com/HB674/25_HC236/blob/main/assets/member/howon.jpeg" > | <img width="173" height="228" alt="image" src="https://github.com/HB674/25_HC236/blob/main/assets/member/euntaek.jpeg" > | <img width="173" height="228" alt="image" src="https://github.com/HB674/25_HC236/blob/main/assets/member/hanbeom.jpeg" > | <img width="173" height="228" alt="image" src="https://github.com/HB674/25_HC236/blob/main/assets/member/joonwoo.jpeg" > | <img width="173" height="228" alt="image" src="https://github.com/HB674/25_HC236/blob/main/assets/member/mento.jpeg" > |
|:---:|:---:|:---:|:---:|:---:|
| **김호원** | **임은택** | **전한범** | **최준우** | **권용원** |
| • 프론트엔드 <br> • 딥페이크 | • 딥보이스 <br> • 영상 제작 | • 백엔드 <br> • API 개발 |• 팀장 <br> • 딥페이크 | • 프로젝트 멘토 <br> • 기술 자문 |




---
## **💡3. 시스템 구성도**

- S/W 구성도
<img width="720" height="990" alt="image" src="https://github.com/HB674/25_HC236/blob/main/assets/sw.jpg" />



- 서비스 흐름도
<img width="959" height="576" alt="image" src="https://github.com/HB674/25_HC236/blob/main/assets/sevice.png" />


---
## **💡4. 작품 소개영상**

[![한이음 드림업 프로젝트 소개](https://github.com/HB674/25_HC236/blob/main/assets/youtubeimage.jpg)](youtube.com/watch?v=X-O7OI657Z0&feature=youtu.be)


---
## **💡5. 핵심 소스코드**
- 소스코드 설명 : OpenAI TTS API로 음성을 생성한 뒤, 이를 AudioJob 파이프라인에 등록합니다. AudioJob은 Applio(Voice Conversion), SadTalker, Wav2Lip, GFPGAN 등 후속 모듈을 순차 실행하여 최종 딥보이스·딥페이크 결과물을 생성·저장합니다.

```Python
async def create_tts_audio_job(req: TTSJobRequest):
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
    
    if req.voice_profile:
        chosen_voice = _tts_voice_from_profile(req.voice_profile, fallback="onyx")
    else:
        chosen_voice = req.voice or "onyx"

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

    audio_req = AudioJobRequest(
        audio_path=rel,
        image_path=str(image_in.resolve().relative_to(SHARED_DIR.resolve())),
        use_applio=req.use_applio,
        pitch=mapped_pitch,
        voice_profile=req.voice_profile,
        pth_path=req.pth_path,
        index_path=req.index_path,
    )

    job = _new_job_state(audio_req)
    job.params["tts_mapped_pitch"] = mapped_pitch
    job.params["tts"] = {
        "voice": chosen_voice,
        "response_format": req.response_format or "mp3",
        "auto_ssml_wrap": bool(req.auto_ssml_wrap if req.auto_ssml_wrap is not None else True),
        "text_source": text_source, 
        "output_basename": req.output_basename,
    }
    job.params["image_auto_selected"] = image_auto
    job.artifacts["tts_audio"] = rel                
    job.artifacts["tts_audio_abs"] = out or None     
    job.timings["openaitts"] = ms

    JOBS[job.job_id] = job
    asyncio.create_task(_run_audio_job(job))
    return {"job_id": job.job_id, "status": job.status}
