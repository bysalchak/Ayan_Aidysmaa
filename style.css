(() => {
  "use strict";

  const $ = id => document.getElementById(id);

  const cover = $("cover");
  const invitation = $("invitation");
  const music = $("music");
  const musicButton = $("musicButton");

  const reducedMotion = matchMedia(
    "(prefers-reduced-motion: reduce)"
  ).matches;

  const weddingDate = Date.parse(
    "2026-11-14T17:00:00+07:00"
  );

  let opened = false;
  let calendarCelebrated = false;

  document.body.classList.add("locked");

  music.volume = 0.55;

  // МУЗЫКА
  function updateMusicButton() {
    const playing = !music.paused;

    musicButton.textContent = playing
      ? "♫ Пауза"
      : "♫ Включить";

    musicButton.setAttribute(
      "aria-label",
      playing
        ? "Приостановить музыку"
        : "Включить музыку"
    );
  }

  function startMusic() {
    musicButton.hidden = false;

    const playback = music.play();

    if (playback) {
      playback.catch(updateMusicButton);
    }
  }

  music.addEventListener("play", updateMusicButton);
  music.addEventListener("pause", updateMusicButton);
  music.addEventListener("error", updateMusicButton);

  musicButton.addEventListener("click", () => {
    if (music.paused) {
      startMusic();
    } else {
      music.pause();
    }
  });

  // ОТКРЫТИЕ ПРИГЛАШЕНИЯ
  function openInvitation() {
    if (opened) return;

    opened = true;

    startMusic();

    $("openEnvelope").disabled = true;
    $("openButton").disabled = true;

    invitation.hidden = false;
    invitation.inert = true;

    window.scrollTo(0, 0);

    requestAnimationFrame(() => {
      cover.classList.add("opening");
    });

    setTimeout(() => {
      cover.hidden = true;
      invitation.inert = false;

      document.body.classList.remove("locked");

      window.scrollTo(0, 0);

      $("heroTitle").focus({
        preventScroll: true
      });

      observeCalendar();
    }, reducedMotion ? 0 : 1600);
  }

  $("openEnvelope").addEventListener(
    "click",
    openInvitation
  );

  $("openButton").addEventListener(
    "click",
    openInvitation
  );

  // КАЛЕНДАРЬ: НОЯБРЬ 2026
  const days = $("days");

  // 1 ноября — воскресенье, поэтому 6 пустых клеток.
  for (let i = 0; i < 6; i++) {
    const empty = document.createElement("span");

    empty.setAttribute("aria-hidden", "true");

    days.appendChild(empty);
  }

  for (let day = 1; day <= 30; day++) {
    const element = document.createElement(
      day === 14 ? "button" : "span"
    );

    element.textContent = day;

    if (day === 14) {
      element.type = "button";

      element.setAttribute(
        "aria-label",
        "14 ноября — день нашей свадьбы"
      );

      element.addEventListener("click", celebrate);
    }

    days.appendChild(element);
  }

  // ОБРАТНЫЙ ОТСЧЁТ
  function updateCountdown() {
    const remaining = Math.max(
      0,
      Math.floor((weddingDate - Date.now()) / 1000)
    );

    const values = {
      d: Math.floor(remaining / 86400),
      h: Math.floor(remaining / 3600) % 24,
      m: Math.floor(remaining / 60) % 60,
      s: remaining % 60
    };

    Object.entries(values).forEach(([id, value]) => {
      $(id).textContent = String(value).padStart(2, "0");
    });

    $("countTitle").textContent =
      Date.now() < weddingDate
        ? "До начала торжества осталось"
        : "Наш счастливый день — 14 ноября 2026";
  }

  updateCountdown();

  setInterval(updateCountdown, 1000);

  // ОТВЕТ ГОСТЯ
  const form = $("rsvpForm");
  const attendance = $("attendance");
  const guestCount = $("guestCount");
  const smsLink = $("smsLink");
  const formStatus = $("formStatus");

  function clearPreparedAnswer() {
    smsLink.hidden = true;
    smsLink.removeAttribute("href");
    formStatus.textContent = "";
  }

  form.addEventListener("input", clearPreparedAnswer);
  form.addEventListener("change", clearPreparedAnswer);

  attendance.addEventListener("change", () => {
    const coming = attendance.value === "yes";

    guestCount.disabled = !coming;
    guestCount.required = coming;
  });

  $("guestName").addEventListener("input", () => {
    $("guestName").setCustomValidity("");
  });

  form.addEventListener("submit", event => {
    event.preventDefault();

    const name = $("guestName").value.trim();

    if (!name) {
      $("guestName").setCustomValidity(
        "Пожалуйста, укажите ваше имя."
      );

      $("guestName").reportValidity();

      return;
    }

    const answers = {
      yes: "Да, приду / придём",
      no: "К сожалению, не смогу",
      later: "Сообщу чуть позже"
    };

    const lines = [
      "Свадьба Аяна и Айдысмаа — 14.11.2026",
      "Гость: " + name,
      "Ответ: " + answers[attendance.value]
    ];

    if (attendance.value === "yes") {
      lines.push(
        "Количество гостей: " + guestCount.value
      );
    }

    const appleDevice =
      /iPhone|iPad|iPod/.test(navigator.userAgent) ||
      (
        navigator.platform === "MacIntel" &&
        navigator.maxTouchPoints > 1
      );

    smsLink.href =
      "sms:+79230204444" +
      (appleDevice ? "&" : "?") +
      "body=" +
      encodeURIComponent(lines.join("\n"));

    smsLink.hidden = false;

    formStatus.textContent =
      "Ответ подготовлен. Откройте SMS и отправьте сообщение Айдысмаа.";
  });

  // МЯГКИЙ САЛЮТ У КАЛЕНДАРЯ
  const canvas = $("confetti");
  const ctx = canvas.getContext("2d");

  let particles = [];
  let animationFrame = null;
  let width = innerWidth;
  let height = innerHeight;
  let lastCelebration = 0;
  let previousTime = 0;

  function resizeCanvas() {
    if (!ctx) return;

    width = innerWidth;
    height = innerHeight;

    const ratio = Math.min(devicePixelRatio || 1, 2);

    canvas.width = Math.round(width * ratio);
    canvas.height = Math.round(height * ratio);

    ctx.setTransform(ratio, 0, 0, ratio, 0, 0);
  }

  addEventListener("resize", resizeCanvas);

  resizeCanvas();

  function celebrate() {
    if (
      !ctx ||
      reducedMotion ||
      Date.now() - lastCelebration < 2500
    ) {
      return;
    }

    lastCelebration = Date.now();

    const colors = [
      "#c9ab70",
      "#e7d3a8",
      "#a7b58f",
      "#d6b4a3",
      "#f1e9d2"
    ];

    for (let i = 0; i < 100; i++) {
      const angle = Math.random() * Math.PI * 2;
      const speed = 1 + Math.random() * 4;

      particles.push({
        x: width / 2,
        y: height * 0.3,
        vx: Math.cos(angle) * speed,
        vy: Math.sin(angle) * speed - 1,
        life: 1,
        size: 1.5 + Math.random() * 2,
        color: colors[
          Math.floor(Math.random() * colors.length)
        ]
      });
    }

    if (animationFrame === null) {
      previousTime = 0;
      animationFrame = requestAnimationFrame(draw);
    }
  }

  function draw(time) {
    const step = previousTime
      ? Math.min((time - previousTime) / 16.67, 2)
      : 1;

    previousTime = time;

    ctx.clearRect(0, 0, width, height);

    particles.forEach(particle => {
      particle.x += particle.vx * step;
      particle.y += particle.vy * step;
      particle.vy += 0.035 * step;
      particle.life -= 0.009 * step;

      ctx.globalAlpha = Math.max(0, particle.life);
      ctx.fillStyle = particle.color;

      ctx.beginPath();

      ctx.arc(
        particle.x,
        particle.y,
        particle.size,
        0,
        Math.PI * 2
      );

      ctx.fill();
    });

    ctx.globalAlpha = 1;

    particles = particles.filter(
      particle => particle.life > 0
    );

    if (particles.length) {
      animationFrame = requestAnimationFrame(draw);
    } else {
      ctx.clearRect(0, 0, width, height);
      animationFrame = null;
    }
  }

  function observeCalendar() {
    if (
      reducedMotion ||
      !("IntersectionObserver" in window)
    ) {
      return;
    }

    const observer = new IntersectionObserver(
      entries => {
        entries.forEach(entry => {
          if (
            entry.isIntersecting &&
            !calendarCelebrated
          ) {
            calendarCelebrated = true;
            celebrate();
            observer.disconnect();
          }
        });
      },
      { threshold: 0.35 }
    );

    observer.observe($("calendarSection"));
  }

  updateMusicButton();
})();
(() => {
  const photo = document.querySelector(
    ".family-photo img, .family-frame img"
  );

  if (!photo) return;

  photo.loading = "eager";
  photo.fetchPriority = "high";
  photo.decoding = "async";

  // Резервируем место, чтобы страница не прыгала.
  photo.width = 1024;
  photo.height = 1536;
})();
(() => {
  function updateCallButtons() {
    const contacts = document.querySelector(
      "#invitation .contacts"
    );

    if (!contacts) return;

    const title = contacts.querySelector("h2");

    if (title) {
      title.textContent = "Свяжитесь с нами";
    }

    const people = [
      {
        phone: "+79232635030",
        label: "Позвонить Аяну"
      },
      {
        phone: "+79230204444",
        label: "Позвонить Айдысмаа"
      }
    ];

    people.forEach(person => {
      let link = contacts.querySelector(
        'a[href="tel:' + person.phone + '"]'
      );

      if (!link) {
        link = document.createElement("a");
        contacts.appendChild(link);
      }

      link.className = "contact";
      link.href = "tel:" + person.phone;
      link.textContent = person.label;

      link.setAttribute(
        "aria-label",
        person.label + ", " + person.phone
      );
    });
  }

  if (document.readyState === "loading") {
    document.addEventListener(
      "DOMContentLoaded",
      updateCallButtons,
      { once: true }
    );
  } else {
    updateCallButtons();
  }
})();
