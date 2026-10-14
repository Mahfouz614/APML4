const demoUser = {
  name: "Amina Hassan",
  email: "amina.hassan@school.edu",
  password: "zenith123"
};

const STORAGE_KEY = "zenithacademy-session";

function setSession(email, name) {
  localStorage.setItem(
    STORAGE_KEY,
    JSON.stringify({
      email,
      name,
      authenticated: true,
      timestamp: Date.now()
    })
  );
}

function getSession() {
  const raw = localStorage.getItem(STORAGE_KEY);
  if (!raw) return null;

  try {
    return JSON.parse(raw);
  } catch {
    return null;
  }
}

function clearSession() {
  localStorage.removeItem(STORAGE_KEY);
}

function redirectIfLoggedIn() {
  const session = getSession();
  if (session && session.authenticated) {
    window.location.href = "dashboard.html";
  }
}

function validateEmail(email) {
  return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
}

function setupLoginForm() {
  const form = document.getElementById("loginForm");
  const message = document.getElementById("loginMessage");

  if (!form) return;

  form.addEventListener("submit", (event) => {
    event.preventDefault();

    const emailInput = document.getElementById("schoolEmail");
    const passwordInput = document.getElementById("password");
    const email = emailInput.value.trim();
    const password = passwordInput.value.trim();

    if (!validateEmail(email)) {
      message.textContent = "Please enter a valid school email address.";
      message.classList.add("error");
      return;
    }

    if (!password) {
      message.textContent = "Please enter your password.";
      message.classList.add("error");
      return;
    }

    const isDemoCredentials =
      email.toLowerCase() === demoUser.email.toLowerCase() && password === demoUser.password;

    if (!isDemoCredentials) {
      message.textContent = "You are not authorized. Use the demo school credentials to log in.";
      message.classList.add("error");
      return;
    }

    setSession(email, demoUser.name);
    message.textContent = "Access granted. Redirecting...";
    message.classList.remove("error");
    message.classList.add("success");

    setTimeout(() => {
      window.location.href = "dashboard.html";
    }, 800);
  });
}

function setupDashboard() {
  const session = getSession();
  if (!session || !session.authenticated) {
    window.location.href = "login.html";
    return;
  }

  const nameEl = document.getElementById("studentName");
  const userBadge = document.getElementById("userBadge");
  const logoutBtn = document.getElementById("logoutBtn");

  if (nameEl) nameEl.textContent = session.name.split(" ")[0];
  if (userBadge) userBadge.textContent = session.email;

  if (logoutBtn) {
    logoutBtn.addEventListener("click", () => {
      clearSession();
      window.location.href = "login.html";
    });
  }
}

function setupRevealAnimations() {
  const revealElements = document.querySelectorAll(".reveal");
  const observer = new IntersectionObserver(
    (entries) => {
      entries.forEach((entry) => {
        if (entry.isIntersecting) {
          entry.target.classList.add("visible");
          observer.unobserve(entry.target);
        }
      });
    },
    { threshold: 0.15 }
  );

  revealElements.forEach((element) => observer.observe(element));
}

function init() {
  redirectIfLoggedIn();
  setupLoginForm();
  setupDashboard();
  setupRevealAnimations();
}

init();
