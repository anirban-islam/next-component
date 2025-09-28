# Next Components

A collection of reusable React components built for Next.js applications.

## 🧩 Components

### Navbar Component

A versatile button component with multiple variants.

![Button Component Example](https://i.ibb.co.com/NnrW7xBT/Screenshot-2025-09-28-151908.png)
![Button Component Example](https://i.ibb.co.com/TxQdymfF/Screenshot-2025-09-28-153817.png)

```javascript
import React, { useEffect, useState } from "react";
import LearnTogetherLogo from "../LearnTogetherLogo/LearnTogetherLogo";
import { Link, NavLink } from "react-router"; // Use react-router-dom here
import {
  FaHome,
  FaChalkboardTeacher,
  FaBookOpen,
  FaSignOutAlt,
  FaTachometerAlt,
  FaUserCircle,
  FaSun,
  FaMoon,
} from "react-icons/fa";
import useAuth from "../../hooks/useAuth";
import useRole from "../../hooks/useRole";

const Navbar = () => {
  const { user, logOut } = useAuth();
  const { role } = useRole();

  const handleLogout = () => {
    logOut()
      .then((result) => {
        // console.log(result);
      })
      .catch((error) => {
        console.log(error);
      });
    // console.log('Logging out...');
  };

  const navLinks = (
    <>
      <li>
        <NavLink
          to="/"
          className={({ isActive }) =>
            `flex items-center text-base gap-2 ${
              isActive
                ? "bg-primary text-white font-semibold underline underline-offset-4"
                : ""
            }`
          }
        >
          <FaHome className="inline md:hidden" />
          <span>Home</span>
        </NavLink>
      </li>

      <li>
        <NavLink
          to="/tutors"
          className={({ isActive }) =>
            `flex items-center text-base gap-2 ${
              isActive
                ? "bg-primary text-white font-semibold underline underline-offset-4"
                : ""
            }`
          }
        >
          <FaChalkboardTeacher className="inline md:hidden" />
          <span>Tutor</span>
        </NavLink>
      </li>

      <li>
        <NavLink
          to="/study-sessions"
          className={({ isActive }) =>
            `flex items-center text-base gap-2 ${
              isActive
                ? "bg-primary text-white font-semibold underline underline-offset-4"
                : ""
            }`
          }
        >
          <FaBookOpen className="inline md:hidden" />
          <span>Study Sessions</span>
        </NavLink>
      </li>
    </>
  );

  const [theme, setTheme] = useState("light");
  const [manualTheme, setManualTheme] = useState(false); // track if user toggled theme manually

  // Initialize theme on mount and add system theme listener
  useEffect(() => {
    const savedTheme = localStorage.getItem("theme");
    if (savedTheme) {
      setTheme(savedTheme);
      document.documentElement.setAttribute("data-theme", savedTheme);
      setManualTheme(true);
    } else {
      const prefersDark = window.matchMedia(
        "(prefers-color-scheme: dark)"
      ).matches;
      const defaultTheme = prefersDark ? "dark" : "light";
      setTheme(defaultTheme);
      document.documentElement.setAttribute("data-theme", defaultTheme);
    }

    const mediaQuery = window.matchMedia("(prefers-color-scheme: dark)");
    const systemThemeChangeHandler = (e) => {
      if (!manualTheme) {
        const newTheme = e.matches ? "dark" : "light";
        setTheme(newTheme);
        document.documentElement.setAttribute("data-theme", newTheme);
      }
    };

    mediaQuery.addEventListener("change", systemThemeChangeHandler);

    return () => {
      mediaQuery.removeEventListener("change", systemThemeChangeHandler);
    };
  }, [manualTheme]);

  // Theme toggle handler
  const toggleTheme = () => {
    const newTheme = theme === "light" ? "dark" : "light";
    setTheme(newTheme);
    setManualTheme(true);
    document.documentElement.setAttribute("data-theme", newTheme);
    localStorage.setItem("theme", newTheme);
  };

  return (
    <div className="backdrop-blur-2xl bg-base-200/80">
      <div className="navbar md:w-10/12 w-11/12 mx-auto py-3 ">
        <div className="navbar-start">
          {/* Mobile device */}
          <LearnTogetherLogo />
        </div>

        {/* large device */}
        <div className="navbar-center hidden lg:flex font-semibold bg-base-100 px-4 rounded-xl">
          <ul className="menu menu-horizontal px-1 gap-2 text-base">
            {navLinks}
          </ul>
        </div>

        {/* Right side (profile or login + theme toggle) */}
        <div className="navbar-end flex items-center gap-3">
          <div className="hidden md:block">
            {user && (
              <Link to="/become-tutor">
                <button className="btn btn-primary px-6 py-2 hover:scale-105 transition-transform duration-300">
                  Join as Tutor
                </button>
              </Link>
            )}
          </div>
          {/* Theme toggle button */}
          <button
            onClick={toggleTheme}
            className="btn btn-ghost btn-circle"
            aria-label="Toggle Dark Mode"
            title="Toggle Dark Mode"
          >
            {theme === "light" ? <FaMoon size={20} /> : <FaSun size={20} />}
          </button>

          {user ? (
            <div className="dropdown dropdown-end">
              <div
                tabIndex={0}
                role="button"
                className="btn btn-ghost btn-circle avatar"
              >
                <div className="w-10 rounded-full ring ring-primary ring-offset-base-100 ring-offset-2">
                  <img src={user.photoURL} alt="User Profile" />
                </div>
              </div>
              <ul
                tabIndex={0}
                className="mt-3 z-50 p-3 shadow menu menu-sm dropdown-content bg-base-100 rounded-box w-52"
              >
                <li className="mb-1 text-center font-bold flex items-center justify-center gap-1">
                  <FaUserCircle /> {user.displayName}
                </li>

                {/* Mobile navLinks inside dropdown */}
                <div className="lg:hidden">{navLinks}</div>
                {/* <ul className="lg:hidden text-base">
                                    {navLinks}
                                </ul> */}

                <li>
                  <Link
                    to="/dashboard"
                    className="flex items-center text-base gap-2"
                  >
                    <FaTachometerAlt className="inline lg:hidden" />
                    <span>Dashboard</span>
                  </Link>
                </li>
                <li>
                  <button
                    onClick={handleLogout}
                    className="flex items-center text-base gap-2"
                  >
                    <FaSignOutAlt className="inline lg:hidden" />
                    <span>Logout</span>
                  </button>
                </li>
              </ul>
            </div>
          ) : (
            <div className="flex gap-2">
              <Link to="/login" className="btn btn-primary md:text-base">
                Login
              </Link>

              <Link
                to="/register"
                className="btn bg-base-100 hover:border hover:border-primary md:text-base hidden md:flex"
              >
                Register
              </Link>
            </div>
          )}
        </div>
      </div>
    </div>
  );
};

export default Navbar;
```

<!-- ## 🤝 Contributing

1. Fork the project
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Add your component following the structure above
4. Include proper documentation and images
5. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
6. Push to the branch (`git push origin feature/AmazingFeature`)
7. Open a Pull Request -->

### Contribution Guidelines

- Follow the existing code style and structure
<!-- - Include TypeScript definitions if applicable -->
- Add tests for new components
- Update documentation with examples and images
- Ensure all existing tests pass

<!-- ## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details. -->

## 📧 Support

If you have any questions or need help, please:

- Open an issue on [GitHub Issues](https://github.com/yourusername/next-components/issues)
<!-- - Check the [documentation](./docs) -->
- Contact the maintainers

---

Made with ❤️ for the Next.js community
